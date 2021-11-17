# pull latest commit for each branch
```shell

echo "CN所有分支已经为最新" > /tmp/git_upstream_status_delta_branches

#使用时需要更改项目路径
cd ~/Desktop/Projects/thecn.com/www/testcn


git fetch --prune origin

currentBranch=$(git rev-parse --abbrev-ref HEAD)

git for-each-ref --format="%(refname:short) %(upstream:short)" refs/heads | \
while read local remote
do
    [ -z "$remote" ] && continue
    git rev-list --left-right "${local}...${remote}" -- 2>/dev/null >/tmp/git_upstream_status_delta || continue
    RIGHT_AHEAD=$(grep -c '^>' /tmp/git_upstream_status_delta)
    if [ $RIGHT_AHEAD -gt 0 ]
    then
        echo "(behind $RIGHT_AHEAD) $local"
        git checkout $local > /dev/null 2>&1
        git pull
        sleep 1
        echo $local >>/tmp/git_upstream_status_delta_branches
    fi
done

branches=$(cat /tmp/git_upstream_status_delta_branches)

git checkout $currentBranch

osascript -e '''on run argv
  display dialog argv with icon caution
end run''' "$branches"

```

# VSCode extension demo 
- [demo code](https://github.com/Mrooze-zeng/test-demo) only support MacOS
- extension [download](https://marketplace.visualstudio.com/items?itemName=mrooze.pull-all)
