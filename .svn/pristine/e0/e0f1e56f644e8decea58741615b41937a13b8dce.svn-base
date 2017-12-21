#!/bin/bash
SVN="svn --username=hd.server"

TOOLS_DIR=".."
WORK_DIR="$TOOLS_DIR/generate"

##########
DATA_CSV_DIR="$WORK_DIR/csv"
DATA_CLIENT_DIR="$WORK_DIR/lua"
DATA_SERVER_DIR="$WORK_DIR/java"
DATA_CHAR_DIR="$WORK_DIR/char"

PROJECT_PREFIX="http://svn.hadoit.com/svn/games/fs"
DATA_CSV_URL="$PROJECT_PREFIX/doc/data/trunk/csv"
DATA_CLIENT_URL="$PROJECT_PREFIX/dev/client/trunk/projects/FsGame/Resources/script_lua/config/data"
DATA_SERVER_URL="$PROJECT_PREFIX/dev/server/trunk/src/main/java/com/hadoit/game/fsgame/config/data"
DATA_CHAR_URL="$PROJECT_PREFIX/art/font/char"

svn_co_or_up()
{
if [ ! -d $1 ] ; then
    $SVN co $2 $1
    if [ $? != 0 ] ; then
        echo "can't check out $1, exit!"
        exit 1
    fi
else
    $SVN up --accept 'theirs-full' $1
    if [ $? != 0 ] ; then
        echo "can't update $1, exit!"
        exit 1
    fi
fi
}

echo "开始导表..."
#####svn up#####
echo "svn更新..."
if [ ! -d $WORK_DIR ]; then 
    mkdir $WORK_DIR
fi
svn up $TOOLS_DIR
svn_co_or_up $DATA_CSV_DIR $DATA_CSV_URL
svn_co_or_up $DATA_CLIENT_DIR $DATA_CLIENT_URL
svn_co_or_up $DATA_SERVER_DIR $DATA_SERVER_URL
svn_co_or_up $DATA_CHAR_DIR $DATA_CHAR_URL

#####csv2code#####
new_csv=`$SVN st $DATA_CSV_DIR/*.csv | wc -l`
if [ $new_csv -gt 0 ]; then
   echo "$new_csv 个csv文件需要做提交处理"
   $SVN add $DATA_CSV_DIR/*.csv --force -q
   $SVN commit $DATA_CSV_DIR/*.csv -m "#misc commit csv files"
fi
echo "代码生成..."
python $TOOLS_DIR/csv2code.py $DATA_CSV_DIR $WORK_DIR
success=$?
if [ $success -gt 0 ]; then
    echo '导表出错!'
    exit
fi
#####svn commit#####
REV=`$SVN info $PROJECT_PREFIX | grep -E '^版本: [0-9]+|^Revision: [0-9]+' | cut -d ' ' -f 2`
if [ ! $REV -gt 0 ]; then
    echo "自动生成svn版本号出错: $REV"
    exit
else
    echo "自动生成svn版本号: $REV"
fi

CODE_FILE="$DATA_CLIENT_DIR/*.lua $DATA_SERVER_DIR/*.java $DATA_CHAR_DIR/*.txt"
new_code=`$SVN st $CODE_FILE | wc -l`
if [ $new_code -gt 0 ]; then
    echo "$new_code 个文件需要处理"
    $SVN add $CODE_FILE --force -q
    $SVN commit $DATA_CLIENT_DIR/*.lua -m "#misc commit client design data"
    $SVN commit $DATA_SERVER_DIR/*.java -m "#misc commit server design data"
    $SVN commit $DATA_CHAR_DIR/*.txt -m "#misc commit char art data"
    echo "svn ci 完毕,导表成功!"
else
    echo "没有任何文件需要处理,导表结束!"
fi
