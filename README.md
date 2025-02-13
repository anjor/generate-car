# generate-car
A simple CLI to generate car file and compute commp at the same time.

[![Pull Request](https://github.com/tech-greedy/generate-car/actions/workflows/pull-request.yml/badge.svg)](https://github.com/tech-greedy/generate-car/actions/workflows/pull-request.yml)

### Installation
```shell
$ git clone https://github.com/tech-greedy/generate-car.git
$ cd generate-car
$ go build
```

### Usage
```shell
$ ./generate-car -h
NAME:
   generate-car - generate car archive from list of files and compute commp in the mean time

USAGE:
   generate-car [global options] command [command options] [arguments...]

COMMANDS:
   help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --single                      When enabled, it indicates that the input is a single file or folder to be included in full, instead of a spec JSON (default: false)
   --input value, -i value       When --single is specified, this is the file or folder to be included in full. Otherwise this is a JSON file containing the list of files to be included in the car archive (default: "-")
   --piece-size value, -s value  Target piece size, default to minimum possible value (default: 0)
   --out-dir value, -o value     Output directory to save the car file (default: ".")
   --tmp-dir value, -t value     Optionally copy the files to a temporary (and much faster) directory
   --parent value, -p value      Parent path of the dataset
   --help, -h                    show help (default: false)
```

When `--single` is specified, the input is a single file or folder to be included in full, instead of a spec JSON.
```shell
# Generate car file from a single file
$ generate-car --single -i test_path/test_file. -o out_dir -p test_path
# Generate car file from a single folder
$ generate-car --single -i test_path/test_folder -o out_dir -p test_path
```

For advanced user, without specifying `--single` the input file needs to be a json file that contains a list of file information SORTED by the path. This is useful if you only want to include specific files within a directory or only part of a large file. i.e.
```json
[
  {
    "Path": "test/test.txt",
    "Size": 4038,
    "Start": 1000, # Inclusive
    "End": 2000 # Exclusive
  },
  {
    "Path": "test/test2.txt",
    "Size": 3089
  }
]
```

The output JSON dump contains `DataCid`, `PieceCid` and `PieceSize` which can be used to make a deal with Filecoin storage providers.

All files are read twice hence if the dataset source is on slow storage such as NFS or S3FS/Goofys mount, you may use tmpdir to copy the files to a fast local directory first.

