#Golang develop suggestions

###Git suggestions

`post-commit` hook

We suggest you add the following hook to your `.git/hooks/pre-commit` and `chmod +x` it.

```
#!/bin/sh
# Modified 2015, Austin Dizzy
# Copyright 2012 The Go Authors. All rights reserved.
# Use of this source code is governed by a BSD-style
# license that can be found in the LICENSE file.

# git gofmt pre-commit hook
#
# To use, store as .git/hooks/pre-commit inside your repository and make sure
# it has execute permissions.
#
# This script does not handle file names that contain spaces.

gofiles=$(git diff --cached --name-only --diff-filter=ACM | grep '.go$')
[ -z "$gofiles" ] && exit 0

unformatted=$(gofmt -l $gofiles)
[ -z "$unformatted" ] && exit 0

# Some files are not gofmt'd. Print message and fail.

echo >&2 "The following Go files are not gofmt'd:"
for fn in $unformatted; do
    echo >&2 "  [*] $fn"
done

exec < /dev/tty

read -p "Would you like to gofmt them? (Y/n) " yn
if [ "$yn" = "" ]; then
yn='Y'
fi
case $yn in
  [Yy]) 
    for fn in $unformatted; do
        eval "gofmt -w $PWD/$fn"
        eval "git add $PWD/$fn"
    done
    ;;
  [Nn]) exit 0
    ;;
  * ) echo "Please answer y or n for yes or no.";;
esac

exec <&-

exit 0
```