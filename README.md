# NAME
	tap - A TAP-producing BASH library

![UI](https://github.com/nkh/bash-tap/blob/main/media/tap.png)

# DESCRIPTION

Note: this is not, I believe, 100% up to the latest TAP specification yet, working on it ...

[TAP specifications](https://testanything.org/tap-version-14-specification.html)

Check *SEE ALSO* if you need a full blown test framework.

If you want a minimal, single file, low line count, compact, "get out of my way" lib that you don't even want to look at, this may be for you.

After the tests are run, a summary is displayed; you can also use other TAP consumer to run your tests (IE: prove)

# INSTALL

Single file **tap**, put it somewhere you can find it, source it. 

# API

## Planning tests

```bash
plan_tests    $NUMBER_OF_TESTS   # plan number of tests
plan_no_plan                     # tell framework that it's OK not to plan the number of tests
plan_skip_all [$REASON]          # skip all the tests
```

## Testing functions

```bash
ok     $RESULT ["NAME"]
okx    command arguments | other_command ...
is     $RESULT $EXPECTED ["NAME"]
isnt   $RESULT $EXPECTED ["NAME"]
like   $RESULT $PATTERN ["NAME"]
unlike $RESULT $PATTERN ["NAME"]
pass   ["NAME"]
fail   ["NAME"]
```

## Skipping tests

```bash
skip [$CONDITION] ["REASON"] [$NUMBER_OF_TESTS=1]

skip $feature_not_present "feature not present" 2 ||
	{
	is "$a" "a"
	is "$b" "b"
	}
```

## Future tests

```bash
TODO="not implemented yet" 
	ok ...
	ok $result "some not implemented test"
	is ...
unset TODO
```

## Diagnostic message

```bash
diag 'message'
```

# EXAMPLE

```
source ./tap  # make TAP functions availabe

create_tree() # custom helper function
{
...
# make call to the helper stand out in tests
diag '─' '┌'
diag " test framework: creating new test directory structure in '$1'" '│'
diag '─' '└'
}

plan_tests 13 # we expect 13 tests to be run


# ----------------------------------------------------------------------------

td="test_directory" # where to create the tree
create_tree "$td"

[[ -d $td ]]
ok $? 'Test directory created'

# same test
[[ -d $td ]] && pass 'test directory created' 

tree $td --noreport -C -T '' -H "file://$PWD/$td" -o $td/tree.html
ok $? 'tree -H'

# or let tap execute the command
okx tree $td --noreport -C -T '' -H "file://$PWD/$td" -o $td/tree.html

[[ -f $td/tree.html && -s $td/tree.html ]]
ok $? 'tree.html file created'

# ----------------------------------------------------------------------------

create_tree "$td"

# Set the depth of generation to less than directories depth
tree $td -R -L 2 --noreport -C -T '' -H "file://$PWD/$td" -o $td/tree.html

sub_html=$(find $td -name '00Tree.html' | wc -l) 
is $sub_html 1 'one 00Tree.html found, options: -R -L 2'

expected="file://$PWD/$td/Dockerfile\""
like "$(grep "$expected" $td/tree.html)" "$expected" 'tree.html contains file path for Dockerfile'

# you can display more information in case of error
expected="..."
like "..." "$expected" 'name of test' || diag_lines "text:"$'\n'"$(generate more information)" 

```

# SEE ALSO 

[bash-tap-function](https://github.com/goozbach/bash-tap-functions)

[bash-tap](https://github.com/illusori/bash-tap)

[TAP shell producers](https://testanything.org/producers.html#shell)

[BATS](https://github.com/bats-core)

[BATS article](https://medium.com/@gabriel.starczewski/using-bash-automated-testing-system-to-test-your-azure-pipelines-custom-logic-9be4251b9d55)

# AUTHORS

    Khemir Nadim ibn Hamouda
    https://github.com/nkh
    CPAN ID: NKH

# LICENCE

© Nadim Khemir 2023, Artistic licence 2.0

