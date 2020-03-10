## Download Link

`https://u.mituo.cn/api/metinfo/download/7.0.0`

### Issue detail    

The **path** parameter is vulnerable to path traversal attacks,     enabling read access to arbitrary files on the server.

The payload **../../../../../../../../../../../../../../../../etc/passwd**     was submitted in the path parameter. The requested file was returned in     the application's response.

###       Issue background    

​      File path traversal vulnerabilities arise when user-controllable data is       used within a filesystem operation in an unsafe manner. Typically, a       user-supplied filename is appended to a directory prefix in order to       read or write the contents of a file. If vulnerable, an attacker can       supply path traversal sequences (using dot-dot-slash characters) to       break out of the intended directory and read or write files elsewhere on       the filesystem.    

​      This is typically a very serious vulnerability, enabling an attacker to       access sensitive files containing configuration data, passwords,       database records, log data, source code, and program scripts and       binaries.    

###       Issue remediation    

​      Ideally, application functionality should be designed in such a way that       user-controllable data does not need to be passed to filesystem       operations. This can normally be achieved by referencing known files via       an index number rather than their name, and using application-generated       filenames to save user-supplied file content.    

​      If it is considered unavoidable to pass user-controllable data to a       filesystem operation, three layers of defense can be employed to prevent       path traversal attacks:    

- ​        User-controllable data should be strictly validated before being         passed to any filesystem operation. In particular, input containing         dot-dot sequences should be blocked.      
- ​        After validating user input, the application can use a suitable         filesystem API to verify that the file to be accessed is actually         located within the base directory used by the application. In Java,         this can be achieved by instantiating a java.io.File object using the         user-supplied filename and then calling the getCanonicalPath method on         this object. If the string returned by this method does not begin with         the name of the start directory, then the user has somehow bypassed         the application's input filters, and the request should be rejected.         In ASP.NET, the same check can be performed by passing the         user-supplied filename to the System.Io.Path.GetFullPath method and         checking the returned string in the same way as described for Java.      
- ​        The directory used to store files that are accessed using         user-controllable data can be located on a separate logical volume to         other sensitive application and operating system files, so that these         cannot be reached via path traversal attacks. In Unix-based systems,         this can be achieved using a chrooted filesystem; on Windows, this can         be achieved by mounting the base directory as a new logical drive and         using the associated drive letter to access its contents.      

###       References    

- ​        [File          path traversal](https://portswigger.net/web-security/file-path-traversal)      

###       Vulnerability classifications    

- ​        [CWE-22:          Improper Limitation of a Pathname to a Restricted Directory ('Path          Traversal')](https://cwe.mitre.org/data/definitions/22.html)      
- ​        [CWE-23:          Relative Path Traversal](https://cwe.mitre.org/data/definitions/23.html)      
- ​        [CWE-35: Path          Traversal: '.../...//'](https://cwe.mitre.org/data/definitions/35.html)      
- ​        [CWE-36:          Absolute Path Traversal](https://cwe.mitre.org/data/definitions/36.html)      

