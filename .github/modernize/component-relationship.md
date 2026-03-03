# Component Relationship Diagram

This project has a single-class structure (`Ibatis2Mybatus`) with three independent conversion pipelines sharing a common XML writer utility.

## Component Relationships

```mermaid
flowchart TD
    subgraph Entry["Entry Point"]
        Main["main()\nOrchestrates all three\nconversion pipelines"]
    end

    subgraph PublicAPI["Public API Layer - Static Methods"]
        ConvertDao["convertDaoImpl(filesDir)\nAccepts file or directory path\nIterates files for DAO conversion"]
        ConvertConfig["convertConfigXML(xmlDir)\nAccepts file or directory path\nIterates files for config conversion"]
        ConvertSqlMap["convertSqlMapXML(sqlMapXMLDirs)\nAccepts file or directory path\nIterates files for sqlMap conversion"]
    end

    subgraph DaoPipeline["DAO Conversion Pipeline"]
        TransportDao["transportDaoFile(file)\nBufferedReader line-by-line processing\nRegex string replacement for\niBatis to MyBatis API mappings"]
    end

    subgraph ConfigPipeline["Config XML Conversion Pipeline"]
        TransportConfig["transportConfigXml(file)\ndom4j SAXReader parsing\nDOCTYPE replacement\ntypeAlias grouping\ntypeHandler grouping\nsqlMap to mapper renaming"]
    end

    subgraph SqlMapPipeline["SqlMap XML Conversion Pipeline"]
        TransportSqlMap["transportSqlMapXml(file)\ndom4j SAXReader parsing\nDOCTYPE replacement\nRoot element renaming"]
        HandleResultMap["handleResultMapEle(element)\nResultMap attribute conversion\nclass to type renaming\njdbcType attribute removal"]
        HandleSqlOp["handleSqlOperatorEle(element)\nSQL operator attribute conversion\nresultClass to resultType\nparameterClass to parameterType"]
        HandlePattern["handleSqlTextByPattern(text)\nRegex-based SQL text transformations\nLIKE pattern conversion\nParam syntax conversion\nisNotNull and isEqual to if-test\nin-clause TODO marker"]
    end

    subgraph SharedUtil["Shared Utility"]
        WriteXML["writeToXmlFile(file, document)\ndom4j XMLWriter\nUTF-8 encoding\nPretty-print formatting"]
    end

    subgraph FileSystem["File System"]
        InputFiles["Input Files\niBatis DaoImpl java\niBatis sqlMapConfig xml\niBatis sqlMap xml"]
        OutputFiles["Output Files\nOverwritten in-place\nMyBatis 3 format"]
    end

    Main -->|calls| ConvertDao
    Main -->|calls| ConvertConfig
    Main -->|calls| ConvertSqlMap

    ConvertDao -->|delegates each file to| TransportDao
    ConvertConfig -->|delegates each file to| TransportConfig
    ConvertSqlMap -->|delegates each file to| TransportSqlMap

    TransportSqlMap -->|processes resultMap elements via| HandleResultMap
    TransportSqlMap -->|processes SQL operator elements via| HandleSqlOp
    HandleSqlOp -->|applies regex patterns via| HandlePattern

    TransportConfig -->|writes result via| WriteXML
    TransportSqlMap -->|writes result via| WriteXML

    InputFiles -->|read by| TransportDao
    InputFiles -->|read by| TransportConfig
    InputFiles -->|read by| TransportSqlMap
    TransportDao -->|writes directly to| OutputFiles
    WriteXML -->|writes to| OutputFiles
```
