#!/bin/bash

keep_days=  # ツイート保管期間
today=$(date +%Y%m%d)
targ_day=$(($today-$keep_days-1))

targ_dir="/usr/local/bin/kotoriotoko/gathered/"
 

del_directories() {

  directories=$(find "$targ_dir" -name "$1")
  
  for directory in $directories
  do
    echo $directory "deleted"
    rm -rf "$directory"
  done
  
  return 0
}


max_size=$((1000))  # 最大ツイートデータ量(MB)
cur_size=$(($max_size+1))

while [ $cur_size -gt $max_size ]
do
  del_directories $targ_day
  
  targ_day=$(($targ_day+1))

  if [ $targ_day -ge $today ]
  then
    echo "caution! keep today's tweets"
    exit 1
  fi

  cur_size=$(du -ms "$targ_dir" | sed -r -e "s/([0-9]+).+/\1/")
  echo "directory size(MB):" $cur_size "/" $max_size
done
