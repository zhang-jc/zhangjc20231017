title: Hive启用RowLevelFilter后Create Table AS异常
date: 2021-04-19 11:54:54
tags:
- 大数据
- Hive
- Ranger
categories:
- 大数据
- Hive
---

### 版本信息

- Hive 2.1.1
- Ranger 1.2.0
- Hadoop 2.7.3

### 问题现象

Ranger中启用行过滤规则后出现异常信息：Error: Error running query: java.lang.AssertionError: Unexpected type UNEXPECTED (state=,code=0)。如下图：
![异常信息](/uploads/20210419/ctaException.png)

### 问题调查

Hive社区Jira参考信息：<https://issues.apache.org/jira/browse/HIVE-22029>

- org.apache.hadoop.hive.ql.parse.CalcitePlanner：

  - L292-L298：

    ```Java
    // 2. Regen OP plan from optimized AST
    init(false);
    if (cboCtx.type == PreCboCtx.Type.CTAS) {
      // Redo create-table analysis, because it's not part of doPhase1.
      setAST(newAST);
      newAST = reAnalyzeCtasAfterCbo(newAST);
    }
    ```
    
  - L647-L657：

    ```Java
    ASTNode reAnalyzeCtasAfterCbo(ASTNode newAst) throws SemanticException {
      // analyzeCreateTable uses this.ast, but doPhase1 doesn't, so only reset it
      // here.
      newAst = analyzeCreateTable(newAst, getQB(), null);
      if (newAst == null) {
        LOG.error("analyzeCreateTable failed to initialize CTAS after CBO;" + " new ast is "
            + getAST().dump());
        throw new SemanticException("analyzeCreateTable failed to initialize CTAS after CBO");
      }
      return newAst;
    }
    ```
    
  - L590-L604：

    ```Java
    private void set(Type type, ASTNode ast) {
      if (this.type != Type.NONE) {
        STATIC_LOG.warn("Setting " + type + " when already " + this.type + "; node " + ast.dump()
            + " vs old node " + nodeOfInterest.dump());
        this.type = Type.UNEXPECTED;
        return;
      }
      this.type = type;
      this.nodeOfInterest = ast;
    }

    @Override
    void setCTASToken(ASTNode child) {
      set(PreCboCtx.Type.CTAS, child);
    }
    ```

- org.apache.hadoop.hive.ql.parse.SemanticAnalyzer

  - L11573-L11597:
  
    ```Java
    case HiveParser.TOK_QUERY: // CTAS
      if (command_type == CTLT) {
        throw new SemanticException(ErrorMsg.CTAS_CTLT_COEXISTENCE.getMsg());
      }
      if (cols.size() != 0) {
        throw new SemanticException(ErrorMsg.CTAS_COLLST_COEXISTENCE.getMsg());
      }
      if (partCols.size() != 0 || bucketCols.size() != 0) {
        boolean dynPart = HiveConf.getBoolVar(conf, HiveConf.ConfVars.DYNAMICPARTITIONING);
        if (dynPart == false) {
          throw new SemanticException(ErrorMsg.CTAS_PARCOL_COEXISTENCE.getMsg());
        } else {
          // TODO: support dynamic partition for CTAS
          throw new SemanticException(ErrorMsg.CTAS_PARCOL_COEXISTENCE.getMsg());
        }
      }
      if (isExt) {
        throw new SemanticException(ErrorMsg.CTAS_EXTTBL_COEXISTENCE.getMsg());
      }
      command_type = CTAS;
      if (plannerCtx != null) {
        plannerCtx.setCTASToken(child);
      }
      selectStmt = child;
      break;
      ```
      
### 解决方法

禁用CBO可以解决问题。

    ```XML
    <property>
        <name>hive.cbo.enable</name>
        <value>false</value>
    </property>
    ```