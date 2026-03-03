# Architecture Diagram

This diagram illustrates the architecture of the iBatis-to-MyBatis3 migration tool, a standalone Java command-line application that transforms iBatis 2.x configurations and DAO source files into MyBatis 3 compatible format.

## Application Architecture

```mermaid
flowchart TD
    User(["User / CLI Entry Point\n(main method)"])

    subgraph Core["ibatis2MybatisTools - Core Application (Java 8, Maven)"]
        Entry["Ibatis2Mybatus\nEntry Controller"]

        subgraph Converters["Conversion Modules"]
            DaoConv["DAO Impl Converter\nconvertDaoImpl()\nRegex-based Java source transformation\niBatis API -> MyBatis API"]
            ConfigConv["Config XML Converter\nconvertConfigXML()\niBatis sqlMapConfig.xml -> MyBatis config.xml"]
            SqlMapConv["SQL Map XML Converter\nconvertSqlMapXML()\niBatis sqlMap.xml -> MyBatis mapper.xml"]
        end

        subgraph XmlProcessing["XML Processing (dom4j 2.1.3)"]
            SAXParser["SAXReader\nXML Parsing"]
            XMLWriter["XMLWriter\nXML Output with Pretty Print"]
            DOMHelper["DocumentHelper\nDOM Manipulation"]
        end

        subgraph PatternMatching["Pattern Matching (java.util.regex)"]
            RegexEngine["Regex Engine\nLIKE clause replacement\nParameter binding syntax\nConditional tag transformation"]
        end
    end

    subgraph InputFiles["Input Files (File System)"]
        DaoFiles["DAO Impl Java Files\n*.java"]
        ConfigXML["iBatis Config XML\nsqlMapConfig.xml"]
        SqlMapXML["iBatis SQL Map XML\nsqlMap.xml"]
    end

    subgraph OutputFiles["Output Files (File System - in-place overwrite)"]
        OutDao["Updated DAO Java Files\nSqlMapClientDaoSupport -> SqlSessionDaoSupport"]
        OutConfig["MyBatis Config XML\nmybatis-config.xml compatible"]
        OutSqlMap["MyBatis Mapper XML\nmapper.xml compatible"]
    end

    User --> Entry
    Entry --> DaoConv
    Entry --> ConfigConv
    Entry --> SqlMapConv

    DaoConv --> RegexEngine
    ConfigConv --> SAXParser
    SqlMapConv --> SAXParser

    SAXParser --> DOMHelper
    DOMHelper --> XMLWriter

    DaoFiles --> DaoConv
    ConfigXML --> ConfigConv
    SqlMapXML --> SqlMapConv

    RegexEngine --> OutDao
    XMLWriter --> OutConfig
    XMLWriter --> OutSqlMap
```
