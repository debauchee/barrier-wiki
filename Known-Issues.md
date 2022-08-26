# Known Barrier issues

This issues are some of the most common ones reported, and we aim

(Please feel free to make PRs [here][wiki_repo])

- Some AV software detecting Barrier as malware:
  We are aware that some [AV software detects](av_issue) Barrier as
  malware.

  Please be rest assured that Barrier _does not_ contain malware in
  the source code. However, we cannot guarantee that all channels
  where Barrier is distributed are free of malware.

  Please feel free to inspect Barrier's [source code][src_code].

## SSL CERTIFICATE DOES NOT EXIST

- Barrier app on macOS and Windows currently does not generate SSL certificate automatically in server mode.

  Hence, user will find the error `ssl certificate doesn't exist: <somepath>/Barrier.pem`. The `Barrier.pem` file is located differently in macOS and Windows. 

  - macOS 12.5.1
  ```shell
  /Users/<username>/Library/Application Support/barrier/SSL/Barrier.pem
  ```

  - Windows 11 21H2
  
  ```shell
  C:\Users\<username>\AppData\Local\Barrier\SSL\Barrier.pem
  ```

  Use OpenSSL to generate SSL cert with the command below and copy it into the corresponding directory.

  ```shell
  openssl req -x509 -nodes -days 9999 -subj /CN=Barrier -newkey rsa:4096 -keyout Barrier.pem -out Barrier.pem
  ``` 
 
  *Tested under WSL2 Windows 11. All credits to [cfarvidson](https://github.com/debauchee/barrier/issues/231#issuecomment-962421337)* 
  



[wiki_repo]: https://github.com/debauchee/barrier-wiki
[av_issue]: https://github.com/debauchee/barrier/issues/666
[src_code]: https://github.com/debauchee/barrier.git
