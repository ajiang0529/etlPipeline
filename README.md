# High-Performance ETL Pipeline

A Python framework for building efficient ETL (Extract, Transform, Load) pipelines that can process large-scale datasets with optimized performance.

## Features

- **High Performance**: Optimized to reduce processing time by 50% or more compared to traditional approaches
- **Parallel Processing**: Utilizes multi-core architectures to distribute workloads
- **Memory Optimization**: Intelligently manages memory with batch processing and streaming techniques
- **Incremental Loading**: Only processes new or changed data since the last execution
- **Multiple Data Sources**: Support for CSV, databases, APIs, Parquet files, and more
- **Schema Mapping**: Flexible mapping between source and target schemas
- **Data Quality**: Built-in data quality checks and corrections
- **Performance Metrics**: Tracks execution time and resource usage for optimization

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/high-performance-etl.git
cd high-performance-etl

# Create a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

## Dependencies

- pandas
- numpy
- pyarrow
- dask
- sqlalchemy
- requests (for API sources)

## Quick Start

```python
from etl_pipeline import ETLPipeline

# Define source configuration
source_config = {
    "type": "csv",
    "path": "input_data.csv",
    "timestamp_column": "created_at"
}

# Define target configuration
target_config = {
    "type": "database",
    "connection_string": "sqlite:///output_data.db",
    "table": "transformed_data",
    "append": True
}

# Define schema mapping and transformations
schema_mapping = {
    "column_mapping": {
        "id": "user_id",
        "name": "full_name",
        "email": "email_address",
        "registration_date": "created_at"
    },
    "transformations": [
        {"column": "name", "operation": "uppercase"},
        {"column": "email", "operation": "lowercase"},
        {"column": "registration_date", "operation": "to_datetime"}
    ],
    "data_quality": [
        {"column": "id", "type": "not_null", "action": "drop"},
        {"column": "email", "type": "not_null", "action": "drop"}
    ]
}

# Initialize and run the pipeline
pipeline = ETLPipeline(
    source_config=source_config,
    target_config=target_config,
    schema_mapping=schema_mapping,
    batch_size=50000,
    num_workers=4,
    use_caching=True
)

# Run the pipeline
result = pipeline.run_pipeline(incremental=True)
print(f"Pipeline execution result: {result}")
```

## Configuration Options

### Source Configuration

The `source_config` dictionary supports the following data sources:

#### CSV Files
```python
source_config = {
    "type": "csv",
    "path": "path/to/file.csv",
    "timestamp_column": "created_at"  # For incremental loads
}
```

#### Databases
```python
source_config = {
    "type": "database",
    "connection_string": "postgresql://user:password@localhost:5432/database",
    "query": "SELECT * FROM source_table",
    "timestamp_column": "updated_at"  # For incremental loads
}
```

#### APIs
```python
source_config = {
    "type": "api",
    "url": "https://api.example.com/data",
    "headers": {"Authorization": "Bearer token"},
    "params": {"limit": 1000},
    "result_path": "data.items",  # Path to the array in the JSON response
    "timestamp_param": "updated_since"  # For incremental loads
}
```

#### Parquet Files
```python
source_config = {
    "type": "parquet",
    "path": "path/to/file.parquet",  # Can be a directory of parquet files
    "timestamp_column": "timestamp"  # For incremental loads
}
```

### Target Configuration

The `target_config` dictionary supports the following targets:

#### CSV Files
```python
target_config = {
    "type": "csv",
    "path": "path/to/output.csv",
    "append": True  # Append to existing file or overwrite
}
```

#### Databases
```python
target_config = {
    "type": "database",
    "connection_string": "postgresql://user:password@localhost:5432/database",
    "table": "target_table",
    "append": True  # Append to existing table or replace
}
```

#### Parquet Files
```python
target_config = {
    "type": "parquet",
    "path": "path/to/output.parquet",
    "compression": "snappy",  # Compression codec (snappy, gzip, brotli, zstd)
    "append": False  # Append to existing file or overwrite
}
```

### Schema Mapping

The `schema_mapping` dictionary defines how data is transformed:

```python
schema_mapping = {
    # Map source columns to target columns
    "column_mapping": {
        "target_column1": "source_column1",
        "target_column2": "source_column2"
    },
    
    # Apply transformations to columns
    "transformations": [
        {"column": "target_column1", "operation": "uppercase"},
        {"column": "target_column2", "operation": "lowercase"},
        {"column": "target_column3", "operation": "to_datetime"},
        {"column": "target_column4", "operation": "to_int"},
        {"column": "target_column5", "operation": "to_float"},
        {"column": "target_column6", "operation": "strip"},
        {"column": "target_column7", "operation": "fill_na", "parameters": {"value": 0}},
        {"column": "target_column8", "operation": "round", "parameters": {"decimals": 2}}
    ],
    
    # Apply data quality checks
    "data_quality": [
        {"column": "target_column1", "type": "not_null", "action": "drop"},
        {"column": "target_column2", "type": "not_null", "action": "fill", "value": "DEFAULT"},
        {"column": "target_column3", "type": "range", "min": 0, "max": 100},
        {"column": "target_column4", "type": "unique", "action": "drop_duplicates"}
    ]
}
```

## Parallel Processing

For large datasets, you can run the pipeline in parallel:

```python
# Define data partitions (e.g., by date range or region)
data_partitions = [
    {"path": "input_data_part1.csv"},
    {"path": "input_data_part2.csv"},
    {"path": "input_data_part3.csv"}
]

# Run the pipeline in parallel
result = pipeline.run_parallel_pipeline(data_partitions)
```

## Performance Tuning

To optimize performance:

1. Adjust `batch_size` based on available memory and dataset characteristics
2. Set `num_workers` to match the number of available CPU cores
3. Enable `use_caching` for repeated transformations
4. Use incremental loading when possible
5. Choose appropriate data formats (Parquet is generally faster than CSV)
6. Use appropriate compression codecs (Snappy offers good compression/speed balance)

## Logging

The pipeline logs detailed information about its operations:

```python
import logging
logging.basicConfig(level=logging.INFO)
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
