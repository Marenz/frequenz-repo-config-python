# Frequenz Repository Configuration Release Notes

## Summary

<!-- Here goes a general summary of what this release is about -->

## Upgrading

- Once you upgraded, you'll be able to upgrade to `pytest` 8, and indirectly to `sybil` 6, which includes types hints. If you do so, you should remove the `mypy` exception for `sybil` in the `pyproject.toml` file.

  Search for the `tool.mypy.overrides` section and remove the `"sybil", "sybil.*"` enties from the `module` list.

### Cookiecutter template

To upgrade without regenerating the project, you can follow these steps:

- Run the following command to add the new `pylint` ignore rules:

    ```sh
    sed '/  # Checked by flake8/a\  "redefined-outer-name",\n  "unused-import",' pyproject.toml
    ```

- It is recommended to update this rule in your repository to use the new bypass rule for the `Protect version branches` ruleset that allows maintainers to force-merge.

    You can do this by re-importing the ruleset or manually:

    Go to the repository settings -> **Rules** -> **Rulesets** -> **Protect version branches** -> **Bypass list** -> **Add bypass** -> Select **Maintain** role and change the dropdown bypass rule to use **Pull requests** instead of **Always**.

- The `labeler` action was upgraded to 5.0.0. This needs a new configuration file.

    If you haven't diverged much from the default configuration (and you are not using exclusion rules), you can update the configuration file by running this script in the root of your repository:

    ```python
    import sys
    lines = []
    state = "looking"
    with open(".github/labeler.yml", encoding="utf-8") as fin:
        for line in fin:
            if "changed-files:" in line:
                sys.stderr.write("Already fixed, aborting...\n")
                sys.exit(1)
            match state:
                case "looking":
                    if not line.startswith(("#", " ", "\t")) and line.rstrip().endswith(":"):
                        line = f"{line}  - changed-files:\n    - any-glob-to-any-file:\n"
                        state = "in-label"
                case "in-label":
                    if not line.lstrip().startswith("-"):
                        state = "looking"
                    else:
                        line = f"    {line}"
            lines.append(line)
    with open(".github/labeler.yml", "w", encoding="utf-8") as fout:
        fout.writelines(lines)
    ```

    This will update the file in place, you can inspect the changes with `git diff`.

## New Features

- Add support for `pytest` 8.

### Cookiecutter template

- Some checks that are already performed by `flake8` are now disabled in `pylint` to avoid double reporting.
- The repository ruleset `Protect version branches` has been updated to allow repository maintainers to skip protection rules in PRs.
- The `labeler` action was upgraded to 5.0.0, which allows for more complex matching rules.

## Bug Fixes

<!-- Here goes notable bug fixes that are worth a special mention or explanation -->

### Cookiecutter template

<!-- Here bug fixes for cookiecutter specifically -->
