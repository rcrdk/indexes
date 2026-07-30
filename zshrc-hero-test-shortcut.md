# hero-test

Shell helper for running Jest tests in MySide Hero projects. Defined in `~/.zshrc` as part of the **MySide Hero aliases** block.

## Overview

`hero-test` wraps `yarn jest -u` and accepts mixed arguments: directories, files, or Jest name patterns. It classifies each argument automatically and runs the appropriate Jest mode.

| Mode | When used | Jest invocation |
|------|-----------|-----------------|
| **By path** | At least one directory or test file was resolved | `yarn jest -u --runTestsByPath <files...>` |
| **By pattern** | No paths/files, only non-file arguments | `yarn jest -u <patterns...>` |

Path mode takes precedence. If any argument resolves to a file or directory, pattern arguments in the same invocation are ignored.

The `-u` flag updates Jest snapshots.

## Usage

```bash
# Run a single test file
hero-test src/components/Button/Button.test.tsx

# Run all tests under a directory (recursive)
hero-test src/components/Button

# Run tests matching a Jest pattern (file/name filter)
hero-test Button

# Multiple files
hero-test src/foo.test.ts src/bar.spec.tsx

# Multiple directories
hero-test src/components src/utils
```

## Next.js paths with special characters

Next.js App Router paths often include symbols that zsh treats specially: parentheses `()`, brackets `[]`, and `@` for parallel routes. **Always quote these paths** (or escape the symbols). The `noglob` alias only disables glob expansion; it does not protect unquoted `( ) [ ]`.

Use **filesystem paths**, not TypeScript import aliases like `@/components/...` — `hero-test` checks paths on disk with `find`, `-f`, and `-d`.

```bash
# Route group — parentheses are special in zsh
hero-test 'app/(dashboard)/settings/page.test.tsx'
hero-test 'app/(marketing)/about'

# Dynamic segment — brackets are glob character classes
hero-test 'app/products/[id]/page.test.tsx'
hero-test 'app/users/[userId]/profile'

# Catch-all and optional catch-all
hero-test 'app/docs/[...slug]/DocsPage.test.tsx'
hero-test 'app/shop/[[...category]]'

# Parallel route (@folder)
hero-test 'app/@modal/(.)login/LoginModal.test.tsx'
hero-test 'app/@sidebar/default.test.tsx'

# Intercepting route — dots and parentheses together
hero-test 'app/photos/(.)[id]/PhotoModal.test.tsx'

# Colocated tests next to a page/layout
hero-test 'app/(auth)/login/page.test.tsx'
hero-test 'src/app/checkout/[orderId]/confirmation/page.spec.tsx'

# Entire app segment with mixed symbols
hero-test 'app/(shop)/products/[slug]/reviews'
```

**Quoting cheat sheet**

| Symbol | Next.js meaning | Why quote |
|--------|-----------------|-----------|
| `(folder)` | Route group (omitted from URL) | `()` triggers glob qualifiers in zsh |
| `[param]` | Dynamic segment | `[]` is a character-class glob |
| `[...slug]` | Catch-all | Same as above |
| `[[...slug]]` | Optional catch-all | Same as above |
| `@folder` | Parallel route slot | `@` can interact with history expansion |
| `(.)` / `(...)` | Intercepting route | Parentheses + brackets combined |

If a path fails with “no test files found”, verify the quoted path exists (`ls 'app/(dashboard)/settings'`) and that you are in the project root.

## Argument handling

For each argument, `hero-test` applies this logic:

1. **Directory** — recursively finds test files with:
   - `*.spec.ts`, `*.spec.tsx`
   - `*.test.ts`, `*.test.tsx`
2. **Existing file** — added to the run list as-is.
3. **Anything else** — treated as a Jest test name pattern (e.g. `-t` style matching via Jest’s positional args).

If no test files are found and no patterns were given, it prints an error and exits with code `1`:

```
hero-test: no test files found for: <args>
```

## Implementation notes

- **Function:** `__hero-test` (internal)
- **Alias:** `hero-test='noglob __hero-test'`
- **`noglob`:** prevents zsh from expanding globs like `*` before the function runs, so patterns are passed through to Jest unchanged.
- **Requires:** `yarn` and Jest configured in the current project (typically run from the repo root).

## Related Hero aliases

Also defined in `~/.zshrc`:

| Alias | Purpose |
|-------|---------|
| `hero-pull` | Fetch all remotes and rebase pull from `origin main` |
| `hero-clear` | Clear Jest cache and delete merged local branches |
| `hero-check` | Full check: typecheck, lint:fix, format, and all tests |

## Examples by scenario

### Focused file or folder during development

```bash
cd ~/path/to/hero-project
hero-test src/features/checkout
```

### Snapshot update for one suite

```bash
hero-test src/features/checkout/Checkout.test.tsx
```

### Run tests whose names match a substring

```bash
hero-test "renders loading state"
```

### Mixed args (paths win)

```bash
# Runs only the file — "Button" is ignored because a path was found
hero-test src/components/Button/Button.test.tsx Button
```

### Next.js route with dynamic segment

```bash
cd ~/path/to/hero-project
hero-test 'app/listings/[listingId]/ListingDetails.test.tsx'
```

### Next.js route group (all tests under segment)

```bash
hero-test 'app/(dashboard)/account'
```

## Source

```zsh
# Hero test alias for running tests by path or pattern
__hero-test() {
  local -a test_paths=()
  local -a test_patterns=()

  for arg in "$@"; do
    if [[ -d "$arg" ]]; then
      while IFS= read -r -d '' file; do
        test_paths+=("$file")
      done < <(
        find "$arg" \( -name '*.spec.ts' -o -name '*.spec.tsx' -o -name '*.test.ts' -o -name '*.test.tsx' \) -print0
      )
    elif [[ -f "$arg" ]]; then
      test_paths+=("$arg")
    else
      test_patterns+=("$arg")
    fi
  done

  if (( ${#test_paths[@]} > 0 )); then
    yarn jest -u --runTestsByPath "${test_paths[@]}"
    return
  fi

  if (( ${#test_patterns[@]} > 0 )); then
    yarn jest -u "${test_patterns[@]}"
    return
  fi

  echo "hero-test: no test files found for: $*" >&2
  return 1
}

alias hero-test='noglob __hero-test'
```
