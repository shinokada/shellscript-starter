<p align="center">
<a href='https://ko-fi.com/Z8Z2CHALG' target='_blank'><img height='42' style='border:0px;height:42px;' src='https://storage.ko-fi.com/cdn/kofi3.png?v=3' alt='Buy Me a Coffee at ko-fi.com' /></a>
</p>

<h1 align="center">Shellscript Starter</h1>

<p align="center">
<a href="https://shellscript-starter.codewithshin.com/">Shellscript Starter</a>
</p>

<p align="center">
<a href="https://twitter.com/shinokada" rel="nofollow"><img src="https://img.shields.io/badge/created%20by-@shinokada-4BBAAB.svg" alt="Created by Shin Okada"></a>
<a href="https://opensource.org/licenses/MIT" rel="nofollow"><img src="https://img.shields.io/github/license/shinokada/shellscript-starter" alt="License"></a>
</p>

## Getting started

Clone or download the repo.

[Read more details](https://medium.com/mkdir-awesome/a-shell-script-starter-for-small-to-large-projects-d9996f0cce83)

## What in this?

- Unit test
- Structure
- Sample commands
- Utils (text, banner)
- Shell and Bash helpers

## Shebang

Select one of the followings for the `shellscript_starter`:

```
#!/usr/bin/env bash
#!/usr/bin/env zsh
#!/usr/bin/env ksh
#!/usr/bin/env bash
#!/bin/sh
```

## Changing the main script name
The main script name is shellscript_starter. You need to change it to a unique name.

```
# change the directory name
$ mv shellscript_starter my_script
$ cd my_script
# change the main script name
$ mv shellscript_starter my_script
```

Test some commands:

```
$ ./my_script -h
$ ./my_script cmd1 -a -b
$ ./my_script --version
$ ./my_script text_example
```

Make the file executable:

```
$ chmod +x my_script
```

## Setting variables
In the main script, shellscript_starter file, find the variable section, and set your variables:

## Importing files
In the import files section, you will find example codes. You need to import `/lib/getoptions.sh` and `/lib/main_definition.sh` for it to work properly.

If you are going to use subcommands, create new definition files in the `lib` directory and import those files here. More about definition files later.

You don’t need to import your subcommand files here. We will do it in the case statement later.

The repo includes the `utils` , `shell_helpers` , and `bash_helpers` files.

```
# Import files
# shellcheck disable=SC1091
{
    . "${script_dir}/lib/getoptions.sh"
    . "${script_dir}/lib/main_definition.sh"
    . "${script_dir}/lib/utils.sh"
    . "${script_dir}/lib/cmd1_definition.sh"
    . "${script_dir}/lib/cmd2_definition.sh"
    # import only one of helpers file
    # . "${script_dir}/lib/shell_helpers.sh"
    # or
    # . "${script_dir}/lib/bash_helpers.sh"
}
```

### utils.sh
You can use the utils file for all Shells. It has check_cmd, check_bash, text colors and text attribute functions.

### shell_helpers.sh and bash_helpers.sh
Use one of those helper files for your project. If your target is Bash, then import bash_helpers.sh, otherwise, import shell_helpers.sh.

### cmd1_definition.sh, cmd2_definition.sh
These are sample codes. Please use them as references when you create yours.

## Creating subcommand definition files
Create your parser definition files in the /lib directory. See the /lib/cmd1_definition.sh and /lib/cmd2_definition.sh as examples. In your definition files, set command flags, parameters, options, descriptions, and examples.

The following example is from ko1nksm/getoptions.

```
parser_definition_cmd1() {
    # from https://github.com/ko1nksm/getoptions/blob/master/examples/basic.sh
    setup REST plus:true help:usage abbr:true -- \
        "Usage: ${2##*/} [options...] [arguments...]" ''
    msg -- 'getoptions basic example' ''
    msg -- 'Options:'
    flag FLAG_A -a -- "message a"
    flag FLAG_B -b -- "message b"
    flag FLAG_F -f +f --{no-}flag -- "expands to --flag and --no-flag"
    flag FLAG_W --with{out}-flag -- "expands to ---with-flag and --without-flag"
    flag VERBOSE -v --verbose counter:true init:=0 -- "e.g. -vvv is verbose level 3"
    param PARAM -p --param pattern:"foo | bar" -- "accepts --param value / --param=value"
    param NUMBER -n --number validate:number -- "accepts only a number value"
    option OPTION -o --option on:"default" -- "accepts -ovalue / --option=value"
    disp :usage -h --help
}
```

## Checking user environment

### OS
You will find the following code in the shellscript_starter file. If you need to check the OS, you can use it to set OS-dependent variables or commands. Otherwise, you can remove it.

```
# CHECK ENVIRONMENT
# If you need to check OS uncomment this
if [ "$(uname)" = "Linux" ]; then
#   do something
    echo "Your OS is Linux."
elif [ "$(uname)" = "Darwin" ]; then
#   do something else
    echo "Your OS is mac."
fi
```

### check commands
The /lib/utils.sh file has check_cmd() function. Once you imported this file in the import section, you can check if the user has certain commands:

```
check_cmd jq
check_cmd you_dont_have_it
```

### check bash
If your target is Bash, then it is a good idea to check the bash version.

The utils file has the check_bash() command. Check if a user has at least Bash version of 5:

```
check_bash 5
```

## Adding a subcommand
Let’s create a new subcommand called create.

### Step 1
Add your command in the /lib/main_definition.sh file.

```
# example
cmd create -- "Create this and that."
```

### Step 2
Create the lib/create_definition.sh file and import it in the above import section.

```
# in the import section
. "${script_dir}/lib/create_definition.sh"
```

```
parser_definition_create() {
    setup REST plus:true help:usage abbr:true -- \
        "Usage: ${2##*/} [options...] [arguments...]" ''
    msg -- 'create example' ''
    msg -- 'Options:'
    flag FLAG_A -a -- "message a"
    param PARAM -d --dir -- "accepts --dir value / --dir=value"
    disp :usage -h --help
}
```

### Step 3
Create /src/create.sh and write your script.

```
fn_create() {
    # sample code
    echo "FLAG_A: $FLAG_A"
    echo "PARAM: $PARAM"
    echo "My variable VAR1: $VAR1."

    i=0
    while [ $# -gt 0 ] && i=$((i + 1)); do
        echo "$i: $1"
        shift
    done
}
```

### Step 4
At the end of the main script, shellscript_starter file, you will find examples.

```
cmd1)
    eval "$(getoptions parser_definition_cmd1 parse "$0")"
    parse "$@"
    eval "set -- $REST"
    # shellcheck disable=SC1091
    . "${script_dir}/src/cmd1.sh"
    fn_cmd1 "$@"
    ;;
cmd2)
    eval "$(getoptions parser_definition_cmd2 parse "$0")"
    parse "$@"
    eval "set -- $REST"
    # shellcheck disable=SC1091
    . "${script_dir}/src/cmd2.sh"
    fn_cmd2 "$@"
    ;;
```

We are adding the following:

```
Your new subcommand is: create 
Your parser definition file is: lib/create_definition
The lib/cmd3_definition file has: parser_definition_create() function
The src/cmd3.sh file has: a function fn_create()
Add the following:
```

```
create)
    eval "$(getoptions parser_definition_create parse "$0")"
    parse "$@"
    eval "set -- $REST"
    # shellcheck disable=SC1091
    . "${script_dir}/src/create.sh"
    fn_create "$@"
    ;;
```

### Step 5 Test your script

```
$ ./shellscript_starter create -a -d ~/Downloads
```

## Example commands
The starter repo includes three subcommands.

### Text example
This subcommand prints different text colors and types.

```
$ ./shellscript_starter text_example
```

The /src/text_example.sh file uses functions from the /lib/utils.sh that you can use for your project.

### cmd1

This subcommand uses the ko1nksm/getoptions basic example.

```
$ ./shellscript_starter cmd1 -a -b +f --with-flag -vvv -p foo -n 5
```

### cmd2

This subcommand uses the ko1nksm/getoptions advanced example.

```
$ ./shellscript_starter cmd2 -a +b -c -vv -p bar --number 5 --range 50 --pattern foo --blood-type ab --array a,b,c,d --array-posix e,f,g arg1
```

## Helper files
The starter repo includes two helper files. Import only one of the helper files.

```
# shellcheck disable=SC1091
{
    # other files ...
    . "${script_dir}/lib/shell_helpers.sh"
}
```

## Debian APT

If you are creating a Debian package, you need to remove the readlinkf() function and define the script directory as in the following:

```
script_dir="/usr/share/my_script"
```

## Unit testing

The starter uses Shellspec unit testing.

When you run a unit test for bash_helpers_spec.sh, specify the path to bash using the -s parameter.

```
$ shellspec spec/bash_helpers_spec.sh -s "/usr/bin/env bash"
```


## Credits

- [Shellspec](https://github.com/shellspec/shellspec) for unit testing
- [getoptions](https://github.com/ko1nksm/getoptions) for option parser
- [readlinkf](https://github.com/ko1nksm/readlinkf) for POSIX compliant readlink -f
- [Bash helpers](https://github.com/dylanaraps/pure-bash-bible)
- [Shell helpers](https://github.com/dylanaraps/pure-sh-bible)
- [Banners](https://github.com/yousefvand/shellman)
