````md
# GCS → Databricks Volume 実装整理（GCP / Databricks）

## 全体構成

```text
GCS bucket/path
↓
Databricks Storage Credential
↓
External Location
↓
External Volume
↓
/Volumes/<catalog>/<schema>/<volume>/
↓
Python / Spark 読み込み
↓
Delta Table化
↓
SELECT / ML利用
````

---

# 1. GCS 側準備

例：

```text
gs://my-mlops-bucket/logs/
gs://my-mlops-bucket/raw/
gs://my-mlops-bucket/features/
```

Databricks 用 Service Account に権限付与。

## 推奨権限

```text
roles/storage.objectViewer
roles/storage.objectCreator
roles/storage.objectAdmin
```

---

# 2. Storage Credential 作成

```sql
CREATE STORAGE CREDENTIAL gcs_mlops_cred
WITH GOOGLE_SERVICE_ACCOUNT
COMMENT 'Credential for GCS bucket';
```

---

# 3. External Location 作成

```sql
CREATE EXTERNAL LOCATION gcs_mlops_logs
URL 'gs://my-mlops-bucket/logs/'
WITH (STORAGE CREDENTIAL gcs_mlops_cred)
COMMENT 'GCS logs location';
```

---

# 4. Catalog / Schema 作成

```sql
CREATE CATALOG IF NOT EXISTS mlops;
CREATE SCHEMA IF NOT EXISTS mlops.raw;
```

---

# 5. External Volume 作成

```sql
CREATE EXTERNAL VOLUME IF NOT EXISTS mlops.raw.gcs_logs
LOCATION 'gs://my-mlops-bucket/logs/'
COMMENT 'Volume for GCS logs';
```

---

# 6. Databricks 内部パス

```text
/Volumes/mlops/raw/gcs_logs/
```

---

# 7. Python でファイル確認

```python
display(dbutils.fs.ls("/Volumes/mlops/raw/gcs_logs/"))
```

---

# 8. JSONログ読み込み

```python
df = spark.read.json(
    "/Volumes/mlops/raw/gcs_logs/*.json"
)

display(df)
```

---

# 9. CSV読み込み

```python
df = (
    spark.read
    .option("header", True)
    .option("inferSchema", True)
    .csv("/Volumes/mlops/raw/gcs_logs/*.csv")
)

display(df)
```

---

# 10. Parquet読み込み

```python
df = spark.read.parquet(
    "/Volumes/mlops/raw/gcs_logs/*.parquet"
)

display(df)
```

---

# 11. Delta Table 化

```python
df.write \
  .format("delta") \
  .mode("append") \
  .saveAsTable("mlops.raw.app_logs")
```

---

# 12. SQL確認

```sql
SELECT *
FROM mlops.raw.app_logs
LIMIT 100;
```

---

# 黒澤様向け 実務最短フロー

```text
Cloud Run / GCP Job ログ
↓
GCS保存
↓
Databricks Volume参照
↓
Pythonで読込
↓
Delta Table化
↓
分析 / ML Feature / 再学習
```

---

# まず覚えるべき最小コード

## 読み込み

```python
spark.read.json("/Volumes/mlops/raw/gcs_logs/*.json")
```

## テーブル保存

```python
df.write.format("delta").saveAsTable("mlops.raw.logs")
```

## SQL確認

```sql
SELECT COUNT(*) FROM mlops.raw.logs;
```

---

# 黒澤様向け一点結論

Databricksで最初に覚えるべきは、

```text
GCSをVolume化して、
Pythonで読んで、
Delta Tableにする
```

これだけで十分実務レベルです。

```
```
