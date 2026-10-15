#!/usr/bin/env bash
# usage: ./install-game.sh <dump-dir> <install-dir>
set -e
shopt -s nullglob

REPO=$(dirname "$(realpath "$0")")
DUMP=$(realpath "$1")
INSTALL=$(realpath -m "$2")
EXE=$INSTALL/$(jq -r .titleId "$DUMP/sce_sys/param.json").elf
WORK=$(mktemp -d)

for file in "$DUMP"/eboot.bin "$DUMP"/{sce_module,sce_modules,prx}/*; do
  out=$WORK/${file#"$DUMP"/}
  mkdir -p "$(dirname "$out")"
  python3 "$REPO/tools/unwrap_self.py" "$file" "$out" || cp "$file" "$out"
done

mkdir -p "$INSTALL"
rm -rf "$INSTALL/app0"
cp -al "$DUMP" "$INSTALL/app0"
ln -sfn "$REPO/build/core/libs/libs" "$INSTALL/libs"

"$REPO/relinker" "$WORK/eboot.bin" "$EXE"
chmod +x "$EXE"
rm -rf "$WORK"
echo "Run: cd \"$INSTALL\" && ./$(basename "$EXE")"
