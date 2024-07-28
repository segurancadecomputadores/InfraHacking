Curl
========================

## File upload

```
curl -X POST http://10.10.10.198:8080/up.php --upload-file test.txt -v
```

Multipart form data

```
curl -v -F key1=value1 -F upload=@localfilename URL
```

```
curl -4 -X POST http://magic.htb/upload.php -F "submit=Upload Image" -F upload=@Capa_video_infra.jpg -v --proxy http://127.0.0.1:8080
```

```
curl -X PUT http://127.0.0.1:9001/root/.ssh/authorized_keys -d "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCauEbpsah1HJTzPR8GXSyqFYULaqSOM6fBjYRp1qM70GIPTGSin25qS76uzC/vHJi48JdvIipLIYcHOFbkFzL8B44Pcdrrb8SKz6CNoLA4wzArcMRlFR4PwPnPBnFuele0wg3NEgps/JsCGLGTJkymcUdml/AIAcNtFA27ThmuoNp1VMrBMW7gKxYXmHV9Umsd+XVMZXlkasprDdQWjUMkWjdno28jv8fltBxTywvDff1Y0N2dhqtPzuv7NHzgXiwCSASG7VlISdMl8gZ8r1qwF7ufgDivWhUX9HcLTv0EbCpfiyhg20ycOI0tcPbH3p5Qsn9r9IqEkvVXuVIHu5CZO3r7Yc382UQbYgRBp0yoZpeGX7V9tsyGm2JKusxGfyyALUuG2OFioOkgwaz1jR9Yh73fAuvhf3PXi63i1YMMui+M3132YcxwKPnDe74qeAiJ4/2j8tIyXCr9T4Oca/AL+rkkYiToDYeY4lRN9Ai01lbi1ujzuy2QyRktmOR7Xfb3iEKL53QdW9NK3LUllCHNC89oMpk90oyFDUuVSZBlJ+xHHir2avNzzGmaMKUP00a0Kx7f+vsg4fxb1tPjYayiJuedDGqys4R3oH6kdGjPCz6jlbhYz2CStxDb8GhAvqzN0D48qB/7/b0JTsNoj/zSBNdPf0rN00jKUD9/kXXsQw== test@test.com"
```

## Authenticated request

```
curl http://intentions.htb/gallery#/feed -H "Cookie: xxx"
```

## Proxied requests

    curl http://intentions.htb/gallery#/feed --proxy http://192.168.0.200:8080