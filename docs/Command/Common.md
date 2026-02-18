
## 多文件并行解压

```
ls *.rar | xargs -n 1 -P 4 -I {} bash -c '
  f="{}"
  dir="${f%.rar}"
  mkdir -p "$dir"
  unar -p <pass> -o "$dir" "$f"
'
```

