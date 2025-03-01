# `gopkg.in/jo3-l/template.v3`

Fork of [botlabs-gg/template](https://github.com/botlabs-gg/template), which is itself a fork of the Go standard `text/template` library.

Originally this was just meant to hold some feature branches to contribute upstream, but it doesn't seem likely that there is interest in accepting them anytime soon. Thus this branch was created so those self-hosting YAGPDB and interested in the new template features can switch easily if they wish. (This package should be a drop-in replacement for `botlabs-gg/template`.)

[See this commit](https://github.com/jo3-l/yagpdb/commit/5deb3adf7e6bcea01bd70b3959c1d860d1303c0d) for an example of what needs to change in your code.

## Additional features

- **New Go number syntax support:** Upstream change; see [the Go spec](https://go.dev/ref/spec#Integer_literals) for what's allowed.

  _Example:_

  ```
  {{ $b := 0b111001 }}
  {{ $sep := 1_000_000 }}
  ```

- **With-else-if support:** Does what you think it does.

  _Example:_

  ```
  {{ with $c1 }}
  	c1 truthy; set to dot
  {{ else if $c2 }}
  	c2 truthy
  {{ else }}
  	neither c1 nor c2 truthy
  {{ end }}
  ```

- **`break`/`continue` actions in loops:** Does what you think it does.

  _Example:_

  ```
  {{ range seq 0 100 }}
  	{{ if gt . 50 }} {{ break }}
  	{{ else }} {{ continue }}
  	{{ end }}
  {{ end }}
  ```

- **`while` loop action:** Does what you think it does.

  _Example:_

  ```
  {{ $i := 0 }}
  {{ while lt $i 10 }}
  	{{ $i }}
  	{{ $i = add $i 1 }}
  {{ end }}
  ```

- **`return` action and `execTemplate` built-in:** Better support for using associated templates as procedures in templates.

  _Example 1:_

  ```
  {{ define "fac" }}
  	{{ if eq . 0 }}
  		{{ return 1 }}
  	{{ end }}
  	{{ return mult . (execTemplate "fac" (sub . 1)) }}
  {{ end }}
  {{ execTemplate "fac" 5 }}
  ```

  _Example 2:_

  ```
  {{/* `return` can be used at the top-level, in which case it stops the execution of the template */}}
  {{ if not .CmdArgs }} {{ return sendMessage nil "no args" }} {{ end }}
  {{ index .CmdArgs 0 }}
  {{/* not executed if .CmdArgs is empty, so no possibility for index out of bounds */}}
  ```

- **`try`/`catch` action:** Support for gracefully recovering from errors returned from template functions.

  _Example 1:_

  ```
  {{ try }}
  	{{ addReactions "emoji" }}
  {{ catch }}
  	{{/* user probably blocked bot */}}
  	Got error: {{ . }} {{/* dot is set to error */}}
  {{ end }}
  ```
