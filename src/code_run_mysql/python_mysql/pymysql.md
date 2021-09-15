# pymysql

安装：

```bash
pip install pymysql

pipenv install pymysql
```

## Python中用pymysql操作MySQL数据库

```python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
# Project: autohomeBrandData
# Function: demo how to use python to operate mysql
# Author: Crifan Li
# Date: 20180527
# Note:
#   If you want to modify to your mysql and table, you need:
#   (1) change change MysqlDb config to your mysql config
#   (2) change CurrentTableName to your table name
#   (3) change CreateTableSqlTemplate to your sql to create new mysql table fields
#   (4) run py file to execute testMysqlDb to init db and create table
#   (5) if your table field contain more type, edit insert to add more type for "TODO: add more type formatting if necessary"


import pymysql
import pymysql.cursors

CurrentTableName = "tbl_autohome_car_info"
CreateTableSqlTemplate = """CREATE TABLE IF NOT EXISTS `%s` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增，主键',
  `cityDealerPrice` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '经销商参考价',
  `msrpPrice` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '厂商指导价',
  `mainBrand` char(20) NOT NULL DEFAULT '' COMMENT '品牌',
  `subBrand` varchar(20) NOT NULL DEFAULT '' COMMENT '子品牌',
  `brandSerie` varchar(20) NOT NULL DEFAULT '' COMMENT '车系',
  `brandSerieId` varchar(15) NOT NULL DEFAULT '' COMMENT '车系ID',
  `model` varchar(50) NOT NULL DEFAULT '' COMMENT '车型',
  `modelId` varchar(15) NOT NULL DEFAULT '' COMMENT '车型ID',
  `modelStatus` char(5) NOT NULL DEFAULT '' COMMENT '车型状态',
  `url` varchar(200) NOT NULL DEFAULT '' COMMENT '车型url',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;"""


class MysqlDb:
    config = {
        'host': '127.0.0.1',
        'port': 3306,
        'user': 'root',
        'password': 'crifan_mysql',
        'database': 'AutohomeResultdb',
        'charset': "utf8"
    }

    defaultTableName = CurrentTableName
    connection = None

    def __init__(self):
        """init mysql"""
        # 1. connect db first
        if self.connection is None:
            isConnected = self.connect()
            print("Connect mysql return %s" % isConnected)

        # 2. create table for db
        createTableOk = self.createTable(self.defaultTableName)
        print("Create table %s return %s" %(self.defaultTableName, createTableOk))

    def connect(self):
        try:
            self.connection = pymysql.connect(**self.config, cursorclass=pymysql.cursors.DictCursor)
            print("connect mysql ok, self.connection=", self.connection)
            return True
        except pymysql.Error as err:
            print("Connect mysql with config=", self.config, " error=", err)
            return False

    def quoteIdentifier(self, identifier):
        """
            for mysql, it better to quote identifier xxx using backticks to `xxx`
            in case, identifier:
                contain special char, such as space
                or same with system reserved words, like select
        """
        quotedIdentifier = "`%s`" % identifier
        # print("quotedIdentifier=", quotedIdentifier)
        return quotedIdentifier

    def executeSql(self, sqlStr, actionDescription=""):
        print("executeSql: sqlStr=%s, actionDescription=%s" % (sqlStr, actionDescription))

        if self.connection is None:
            print("Please connect mysql first before %s" % actionDescription)
            return False

        cursor = self.connection.cursor()
        print("cursor=", cursor)

        try:
            cursor.execute(sqlStr)
self.connection.commit()
            print("+++ Ok to execute sql %s for %s" % (sqlStr, actionDescription))
            return True
        except pymysql.Error as err:
            print("!!! %s when execute sql %s for %s" % (err, sqlStr, actionDescription))
            return False

    def createTable(self, newTablename):
        print("createTable: newTablename=", newTablename)

        createTableSql = CreateTableSqlTemplate % (newTablename)
        print("createTableSql=", createTableSql)

        return self.executeSql(sqlStr=createTableSql, actionDescription=("Create table %s" % newTablename))

    def dropTable(self, existedTablename):
        print("dropTable: existedTablename=", existedTablename)

        dropTableSql = "DROP TABLE IF EXISTS %s" % (existedTablename)
        print("dropTableSql=", dropTableSql)

        return self.executeSql(sqlStr=dropTableSql, actionDescription=("Drop table %s" % existedTablename))

    # def insert(self, **valueDict):
    def insert(self, valueDict, tablename=defaultTableName):
        """
            inset dict value into mysql table
            makesure the value is dict, and its keys is the key in the table
        """
        print("insert: valueDict=%s, tablename=%s" % (valueDict, tablename))

        dictKeyList = valueDict.keys()
        dictValueList = valueDict.values()
        print("dictKeyList=", dictKeyList, "dictValueList=", dictValueList)

        keyListSql = ", ".join(self.quoteIdentifier(eachKey) for eachKey in dictKeyList)
        print("keyListSql=", keyListSql)
        # valueListSql = ", ".join(eachValue for eachValue in dictValueList)
        valueListSql = ""
        formattedDictValueList = []
        for eachValue in dictValueList:
            # print("eachValue=", eachValue)
            eachValueInSql = ""
            valueType = type(eachValue)
            # print("valueType=", valueType)
            if valueType is str:
                eachValueInSql = '"%s"' % eachValue
            elif valueType is int:
                eachValueInSql = '%d' % eachValue
            # TODO: add more type formatting if necessary
            print("eachValueInSql=", eachValueInSql)
            formattedDictValueList.append(eachValueInSql)

        valueListSql = ", ".join(eachValue for eachValue in formattedDictValueList)
        print("valueListSql=", valueListSql)

        insertSql = """INSERT INTO %s (%s) VALUES (%s)""" % (tablename, keyListSql, valueListSql)
        print("insertSql=", insertSql)
        # INSERT INTO tbl_car_info_test (`url`, `mainBrand`, `subBrand`, `brandSerie`, `brandSerieId`, `model`, `modelId`, `modelStatus`, `cityDealerPrice`, `msrpPrice`) VALUES ("https://www.autohome.com.cn/spec/5872/#pvareaid=2042128", "宝马", "华晨宝马", "宝马3系", "66", "2010款 320i 豪华型", "5872", "停售", 325000, 375000)

        return self.executeSql(sqlStr=insertSql, actionDescription=("Insert value to table %s" % tablename))

    def delete(self, modelId, tablename=defaultTableName):
        """
            delete item from car model id for existing table of autohome car info
        """
        print("delete: modelId=%s, tablename=%s" % (modelId, tablename))

        deleteSql = """DELETE FROM %s WHERE modelId = %s""" % (tablename, modelId)
        print("deleteSql=", deleteSql)

        return self.executeSql(sqlStr=deleteSql, actionDescription=("Delete value from table %s by model id %s" % (tablename, modelId)))

def testMysqlDb():
    """test mysql"""

    testDropTable = True
    testCreateTable = True
    testInsertValue = True
    testDeleteValue = True

    # 1.test connect mysql
    mysqlObj = MysqlDb()
    print("mysqlObj=", mysqlObj)

    # testTablename = "autohome_car_info"
    # testTablename = "tbl_car_info_test"
    testTablename = CurrentTableName
    print("testTablename=", testTablename)

    if testDropTable:
        # 2. test drop table
        dropTableOk = mysqlObj.dropTable(testTablename)
        print("dropTable", testTablename, "return", dropTableOk)

    if testCreateTable:
        # 3. test create table
        createTableOk = mysqlObj.createTable(testTablename)
        print("createTable", testTablename, "return", createTableOk)

    if testInsertValue:
        # 4. test insert value dict
        valueDict = {
            "url": "https://www.autohome.com.cn/spec/5872/#pvareaid=2042128", #车型url
            "mainBrand": "宝马", #品牌
            "subBrand": "华晨宝马", #子品牌
            "brandSerie": "宝马3系", #车系
            "brandSerieId": "66", #车系ID
            "model": "2010款 320i 豪华型", #车型
            "modelId": "5872", #车型ID
            "modelStatus": "停售", #车型状态
            "cityDealerPrice": 325000, #经销商参考价
            "msrpPrice": 375000 # 厂商指导价
        }
        print("valueDict=", valueDict)
        insertOk = mysqlObj.insert(valueDict=valueDict, tablename=testTablename)
        print("insertOk=", insertOk)

    if testDeleteValue:
        toDeleteModelId = "5872"
        deleteOk = mysqlObj.delete(modelId=toDeleteModelId, tablename=testTablename)
        print("deleteOk=", deleteOk)

def testAutohomeResultWorker():
    """just test for create mysql db is ok or not"""
    autohomeResultWorker = AutohomeResultWorker(None, None)
    print("autohomeResultWorker=%s" % autohomeResultWorker)

if __name__ == '__main__':
    testMysqlDb()
    # testAutohomeResultWorker()
```


## Python的Flask中pymysql中mysql返回分页查询结果

```python
userId = parsedArgs["userId"]
if userId is None:
    return genRespFailDict(BadRequest.code, "Empty userId")

if userId <= 0:
    return genRespFailDict(BadRequest.code, "Invalid userId %d" % userId)

pageNumber = parsedArgs["pageNumber"]
if pageNumber < 1:
    return genRespFailDict(BadRequest.code, "Invalid pageNumber %d" % pageNumber)

pageSize = parsedArgs["pageSize"]
pageIndex = pageNumber - 1
offset = pageSize * pageIndex
orderBy = "modifyTime"

totalCount = -1
totalPageNum = -1

totalCountSql = "SELECT COUNT(*) from `user_storybook_list` WHERE `userId`=%d" % userId
totalCountOk, resultDict = sqlConn.executeSql(totalCountSql)
log.debug("%s -> %s, %s", totalCountSql, totalCountOk, resultDict)
if totalCountOk and resultDict["data"]:
    totalCount = resultDict["data"][0]["COUNT(*)"]

if totalCount > 0:
    totalPageNum = int(totalCount / pageSize)

    if (totalCount % pageSize) > 0:
        totalPageNum += 1

    if pageNumber > totalPageNum:
        return genRespFailDict(BadRequest.code, "Current page number %d exceed max page number %d" % (
        pageNumber, totalPageNum))

hasPrev = False
if pageNumber > 1:
    hasPrev = True

hasNext = False
if totalPageNum > 0:
    if pageNumber < totalPageNum:
        hasNext = True

searchByUserSql = "SELECT * FROM `user_storybook_list` WHERE `userId`=%d ORDER BY `%s` ASC LIMIT %d OFFSET %d" % (userId, orderBy, pageSize, offset)
searchByUserOk, resultDict = sqlConn.executeSql(searchByUserSql)
log.debug("%s -> %s, %s", searchByUserSql, searchByUserOk, resultDict)
if searchByUserOk and resultDict["data"]:
    foundItemList = resultDict["data"]

    respData = {
        "evaluationList": foundItemList,
        "curPageNum": pageNumber,
        "numPerPage": pageSize,
        "totalNum": totalCount,
        "totalPageNum": totalPageNum,
        "hasPrev": hasPrev,
        "hasNext": hasNext,
    }

    respDict["data"] = respData
    return jsonify(respDict)
```

返回数据：

```json
{
    "code": 200,
    "data": {
        "curPageNum": 1,
        "evaluationList": [
            {
                "active": "Y",
                "createTime": "Wed, 23 Jan 2019 08:40:58 GMT",
                "id": 5,
                "lastReadDuration": 0,
                "modifyTime": "Wed, 23 Jan 2019 08:40:58 GMT",
                "storybookId": "5bd7bd31bfaa44fe2c73736b",
                "type": 0,
                "userId": 28
            },
            {
                "active": "Y",
                "createTime": "Wed, 23 Jan 2019 08:45:04 GMT",
                "id": 6,
                "lastReadDuration": 0,
                "modifyTime": "Wed, 23 Jan 2019 08:45:04 GMT",
                "storybookId": "5bd7bd31bfaa44fe2c73736c",
                "type": 0,
                "userId": 28
            },
            {
                "active": "Y",
                "createTime": "Wed, 23 Jan 2019 08:45:32 GMT",
                "id": 7,
                "lastReadDuration": 0,
                "modifyTime": "Wed, 23 Jan 2019 08:45:32 GMT",
                "storybookId": "5bd7bd31bfaa44fe2c73736e",
                "type": 0,
                "userId": 28
            }
        ],
        "hasNext": true,
        "hasPrev": false,
        "numPerPage": 3,
        "totalNum": 9,
        "totalPageNum": 3
    },
    "message": "Get user storybook list ok"
}
```

Postman测试效果：

![pymysql_resp_list_postman](../assets/img/pymysql_resp_list_postman.png)
