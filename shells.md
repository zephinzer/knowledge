# Shells

## Variable Assignment

```bash
X=1;
Y="1";
```

### Notes on shell variables

- There are no types in shell: `X=1` and `X="1"` are the same thing.

## Branch and Conditional Jumps

Relevant commands:

- `if`
- `fi`
- `elif`
- `then`
- `else`
- `[`
- `]`
- `[[`
- `]]`

### Usage

```bash
X=1; # change this to 2 to invoke the 'elif'
if [[ "$X" = "1" ]]; then
  printf "It is one.\n";
elif [[ "$X" = "2" ]]; then
  printf "It is two.\n";
fi;
```

### Notes on usage in `sh`

`sh` does not support the `[[` and `]]` commands, use `[` and `]` instead with the appropriate flags.

```bash
X=1; # change this to 2 to invoke the 'elif'
if [ $X -eq 1 ]; then
  printf "It is one.\n";
elif [[ $X -eq 2 ]]; then
  printf "It is two.\n";
fi;
```

## Useful Aliases
> Include this inside your `~/.profile` or specific `.[?]shrc` file

```sh
alias ll='ls -lA';
```