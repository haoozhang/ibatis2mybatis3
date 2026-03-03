# Architecture Diagram

A standalone Java 8 command-line tool that migrates iBatis 2 configurations and DAO implementations to MyBatis 3 format using XML parsing and regex-based text replacement.

## Application Architecture

```mermaid
flowchart TD
    Input1["iBatis DaoImpl Java Files\n(.java)"]
    Input2["iBatis sqlMapConfig XML\n(sqlMapConfig.xml)"]
    Input3["iBatis sqlMap XML Files\n(sqlMap.xml)"]

    subgraph Tool["ibatis2MybatisTools - Java 8 CLI Tool"]
        Main["main()\nEntry Point"]

        subgraph DaoLayer["DAO Conversion Layer"]
            ConvertDao["convertDaoImpl()\nFile/Directory Handler"]
            TransportDao["transportDaoFile()\nRegex-based Text Replacement\n- SqlMapClientDaoSupport to SqlSessionDaoSupport\n- queryForList to selectList\n- queryForObject to selectOne\n- getSqlMapClient to getSqlSession"]
        end

        subgraph ConfigLayer["Config XML Conversion Layer"]
            ConvertConfig["convertConfigXML()\nFile/Directory Handler"]
            TransportConfig["transportConfigXml()\ndom4j XML Transformation\n- typeAlias to typeAliases\n- typeHandler to typeHandlers\n- sqlMap to mappers/mapper\n- Update DOCTYPE to MyBatis 3"]
        end

        subgraph MapperLayer["SqlMap XML Conversion Layer"]
            ConvertSqlMap["convertSqlMapXML()\nFile/Directory Handler"]
            TransportSqlMap["transportSqlMapXml()\ndom4j XML Transformation\n- Update DOCTYPE to MyBatis 3\n- resultClass to resultType\n- parameterClass to parameterType\n- isNotNull/isEqual to if test\n- #param# to hash param"]
        end

        subgraph XMLWriter["XML Writer"]
            WriteXML["writeToXmlFile()\ndom4j XMLWriter\nUTF-8 Pretty Print"]
        end
    end

    subgraph Deps["Dependencies - Maven"]
        Dom4j["dom4j 2.1.3\nXML Parsing and Writing"]
        JavaIO["Java IO/Regex\nFile and Text Processing"]
    end

    Output1["MyBatis 3 DaoImpl Java Files\n(.java)"]
    Output2["MyBatis 3 Configuration XML\n(mybatis-config.xml)"]
    Output3["MyBatis 3 Mapper XML Files\n(mapper.xml)"]

    Input1 --> ConvertDao
    Input2 --> ConvertConfig
    Input3 --> ConvertSqlMap

    Main --> ConvertDao
    Main --> ConvertConfig
    Main --> ConvertSqlMap

    ConvertDao --> TransportDao
    ConvertConfig --> TransportConfig
    ConvertSqlMap --> TransportSqlMap

    TransportConfig --> WriteXML
    TransportSqlMap --> WriteXML

    TransportDao --> Output1
    WriteXML --> Output2
    WriteXML --> Output3

    Dom4j -.->|used by| TransportConfig
    Dom4j -.->|used by| TransportSqlMap
    Dom4j -.->|used by| WriteXML
    JavaIO -.->|used by| TransportDao
```
