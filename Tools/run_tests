#!/bin/bash

BUILD_DIR=Debug
if [[ $# -eq 1 ]]; then
	BUILD_DIR=$1
fi
cd ${BUILD_DIR}/tests
ctest -VV
cd ../..
