

```
.line 312
.local v20, "mY":I
invoke-static {v1}, Lcom/mobisec/upos/FC;->r(Ljava/lang/String;)Ljava/lang/String;
move-result-object v23
invoke-static/range {v23 .. v23}, Lcom/mobisec/upos/FC;->sq(Ljava/lang/String;)J
move-result-wide v23
sget-object v25, Lcom/mobisec/upos/FC;->m:[[J
aget-object v25, v25, v2
aget-wide v26, v25, v20
:try_end_20
.catch Ljava/sql/BatchUpdateException; {:try_start_20 .. :try_end_20} :catch_4c
.catch Ljava/security/cert/CertificateEncodingException; {:try_start_20 .. :try_end_20} :catch_4b
.catch Ljava/util/concurrent/RejectedExecutionException; {:try_start_20 .. :try_end_20} :catch_4a
.catch Ljava/security/GeneralSecurityException; {:try_start_20 .. :try_end_20} :catch_4e
.catch Ljava/lang/Exception; {:try_start_20 .. :try_end_20} :catch_4d
cmp-long v25, v23, v26
if-eqz v25, :cond_12
.line 314
add-int/lit8 v23, v7, 0x1
const/16 v21, 0x0
.end local v7    # "idx":I
.local v23, "idx":I
:try_start_21
aput-boolean v21, v5, v7
:try_end_21
.catch Ljava/sql/BatchUpdateException; {:try_start_21 .. :try_end_21} :catch_35
.catch Ljava/security/cert/CertificateEncodingException; {:try_start_21 .. :try_end_21} :catch_34
.catch Ljava/util/concurrent/RejectedExecutionException; {:try_start_21 .. :try_end_21} :catch_33
.catch Ljava/security/GeneralSecurityException; {:try_start_21 .. :try_end_21} :catch_32
.catch Ljava/lang/Exception; {:try_start_21 .. :try_end_21} :catch_31
move/from16 v7, v23
goto :goto_16
:goto_16
add-int/lit8 v4, v4, 0x1
move-object/from16 v1, p0
goto/16 :goto_14
.local v4, "i":I
.local v7, "idx":I
:goto_14
const/16 v2, 0x1e
if-ge v4, v2, :cond_13
:cond_13
add-int/lit8 v1, v7, -0x1e
.local v1, "i":I
:goto_17
if-ge v1, v7, :cond_15
.line 324
:try_start_22
aget-boolean v2, v5, v1
if-nez v2, :cond_14 
const/4 v2, 0x0
return v2 
.line 322
:cond_14
add-int/lit8 v1, v1, 0x1
goto :goto_17
.line 330
.end local v1    # "i":I
if-ge v1, v7, :cond_15
:cond_15
invoke-static/range {p1 .. p1}, Lcom/mobisec/upos/FC;->h(Ljava/lang/String;)Ljava/lang/String;
move-result-object v1
const-string v2, "4193d9b72a5c4805e9a5cc739f8a8fc23b2890e13b83bb887d96f86c30654a12"
invoke-virtual {v1, v2}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z
move-result v1
:try_end_22
.catch Ljava/sql/BatchUpdateException; {:try_start_22 .. :try_end_22} :catch_4c
.catch Ljava/security/cert/CertificateEncodingException; {:try_start_22 .. :try_end_22} :catch_4b
.catch Ljava/util/concurrent/RejectedExecutionException; {:try_start_22 .. :try_end_22} :catch_4a
.catch Ljava/security/GeneralSecurityException; {:try_start_22 .. :try_end_22} :catch_4e
.catch Ljava/lang/Exception; {:try_start_22 .. :try_end_22} :catch_4d
if-nez v1, :cond_16
.line 334
:cond_16
const/4 v1, 0x1
return v1
```



```
   const/4 v5, 0x1
	xor-int/2addr v0, v5

    const/4 v0, 0x0
```




```
fs: v5 -> boolean array len:c8:200(place)
v11: user input -> flag


MOBISEC{this_is_}
```