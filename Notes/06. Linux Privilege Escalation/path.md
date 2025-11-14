# Path Abuse

## Check PATH
```sh
echo $PATH
env | grep PATH
```

## Create a script inside a writable PATH directory

```sh
echo 'echo "PATH ABUSE!"' > /usr/local/bin/mycommand
chmod +x /usr/local/bin/mycommand
mycommand
```

## Modify PATH (dangerous)

```sh
PATH=.:$PATH
export PATH
echo $PATH
```

## Create a malicious script that overrides a system command

```sh
echo 'echo "PATH ABUSE!"' > ls
chmod +x ls
./ls
```
