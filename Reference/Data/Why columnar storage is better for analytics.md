#data #columnar
### 1. **Faster Query Performance (I/O Efficiency)**

- Analytical queries often need only a few columns (e.g., SELECT name, age FROM users).
    
- **Columnar storage reads only relevant columns**, skipping the rest.
    
- In row-based storage, entire rows (including unnecessary columns) are read from disk.
    
- ➡️ **Less data read = faster query times**.
    

### 2. **Better Compression Ratios**

- Data in a column is of the **same data type** (e.g., all integers, all strings).
    
- Compression algorithms are much more effective on homogeneous data.
    
- ➡️ **Smaller storage footprint, lower I/O**.
    

### 3. **Vectorized Processing**

- Columnar formats allow **vectorized (batch) processing** instead of row-by-row.
    
- This is CPU-efficient and speeds up data scans significantly.
    
- ➡️ **Faster computations for aggregates, filters, group-bys**.
    

### 4. **Efficient Predicate Pushdown**

- Filtering (WHERE conditions) is applied at the storage layer (e.g., WHERE age > 30).
    
- Only relevant blocks of data are loaded into memory.
    
- ➡️ **Reduces CPU and memory usage**.
    

### 5. **Parallelism & Scalability**

- Columns can be read **in parallel across multiple threads/nodes**.
    
- Scales well in distributed systems like Spark, Dremio, Presto, Snowflake.