```
if ($request_filename ~ .*\.(html|html)$) {
    add_header Cache-Control "no-cache,no-store";
}
```