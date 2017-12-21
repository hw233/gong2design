#!/bin/bash
SRC_DIR="../../src"
BIN_DIR="../../bin"

rm -rf $BIN_DIR
mkdir $BIN_DIR

find ${SRC_DIR} -type f -name "*.pyc" | xargs rm -f
cp -rf $SRC_DIR/ $BIN_DIR/  
rm -rf $BIN_DIR/shells
python -m compileall -f ${BIN_DIR}
find ${BIN_DIR} -type f -name "*.py" | xargs rm -f
find ${BIN_DIR} -type f -name ".DS_Store" | xargs rm -f
