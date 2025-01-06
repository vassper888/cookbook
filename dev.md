# DEV

## MOODLE + Docker-compose

***

Nginx + SSL + PHP-FPM + PHP-CLI + NodeJS + npm + Grunt + XDebug + Java + SchemaSpy + Zabbix + Apache Solr + Redis + ClamAV + Python3 + Perl + MySQLTuner.pl + MySQL + phpMyAdmin +  + MOODLE + moodle-plugin-ci + tool\_.... + tool\_.... + Mook MOODLE Data

***

## Grunt

```bash
cd /var/www/moodle
```

```bash
npm install grunt
```

<pre class="language-bash"><code class="lang-bash">cd <a data-footnote-ref href="#user-content-fn-1">local/plugin</a>
</code></pre>

Running tasks for component directory:

```bash
grunt amd
```

If not errors, build `/amd/build/`[`script`](#user-content-fn-2)[^2]`.min.js` and `/amd/build/`[`script`](#user-content-fn-3)[^3]`.min.map.js`

***

## Make new plugin

```bash
cd ~

nano moodle-plugin-init.sh
```

```bash
#!/bin/bash

getopt -l "name:,version::,verbose" -- "n:v::V" --name=Karthik -version=5.2 -verbose

OPTSTRING=":type:name:"

while getopts ${OPTSTRING} opt; do
  case ${opt} in
    x)
      echo "Option --name was triggered, Argument: ${OPTARG}"
      ;;
    y)
      echo "Option --type was triggered, Argument: ${OPTARG}"
      ;;
    :)
      echo "Option -${OPTARG} requires an argument."
      exit 1
      ;;
    ?)
      echo "Invalid option: -${OPTARG}."
      exit 1
      ;;
  esac
done
```

```bash
chmod +x moodle-plugin-init.sh
```

```bash
nano ~/.bashrc
```

```bash
alias moodle-plugin-init='~/moodle-plugin-init.sh'
```

Run in console for test and print help:

```
moodle-plugin-init
```

Get version moodle-plugin-init:

```
moodle-plugin-init --version
```

Init new moodle plugin (Full mode):

```bash
moodle-plugin-init --mod=full --type=local --name=vc --lang=en,ru
```

Init new moodle plugin (Master mode):

```bash
moodle-plugin-init --type=local
    \ --name=vc
    \ --lang=en,ru
    \ --pluginame-en=''
    \ --pluginame-ru=''
    \ --db=true
    \ --css=true
    \ --js-amd=true
    \ --pix=true
    \ --templates=true
    \ --git-init=true
    \ --git-branch=master
    \ --git-remote=
    \ --readme=true
    \ --unit-test=true
    \ --access=true
    \ --event=true
    \ --hooks=true
    \ --messages=true
    \ --services=true
    \ --tasks=true
    \ --cache=true
    \ --uninstall=true
    \ --install=true
    \ --privacy=true
```

[^1]: Your plugin dir

[^2]: Your script name

[^3]: Your script name
