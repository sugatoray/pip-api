
Since [`pip`](https://pypi.org/p/pip) is a command-line-tool, [it does not have
an official, supported, _importable_
API](https://pip.pypa.io/en/latest/user_guide/#using-pip-from-your-program).

However, this does not mean that people haven't tried to `import pip`, usually
to end up with much headache when `pip`'s maintainers do routine refactoring.

## Goal
The goal of this project is to provide an importable `pip` API, which is _fully
compliant_ with the recommended method of using `pip` from your program.

How? By providing an importable API that wraps command-line calls to `pip`,
this library can be used as a drop-in replacement for existing uses of `pip`'s
internal API.

### Scope
This goal means that any new API added here must have the following
equivalents:

- some internal `pip` API (or combination of internal APIs)
- some CLI calls (or combination of CLI calls)

Any functionality that is not currently possible from internal `pip` API or
CLI calls is out of scope.

## Supported Commands

Not all commands are supported in all versions of `pip` and on all platforms.
If the command you are trying to use is not compatible, `pip_api` will raise a
`pip_api.exceptions.Incompatible` exception for your program to catch.

### Available with all `pip` versions:
* `pip_api.version()`
  > Returns the `pip` version as a string, e.g. `"9.0.1"`

* `pip_api.installed_distributions(local=False)`
  > Returns a list of all installed distributions as a `Distribution` object with the following attributes:
  > * `Distribution.name` (`string`): The name of the installed distribution
  > * `Distribution.version` ([`packaging.version.Version`](https://packaging.pypa.io/en/latest/version/#packaging.version.Version)): The version of the installed distribution
  > * `Distribution.location` (`string`): The location of the installed distribution
  > * `Distribution.editable` (`bool`): Whether the distribution is editable or not
  > Optionally takes a `local` parameter to filter out globally-installed packages

* `pip_api.parse_requirements(filename, options=None, include_invalid=False)`
  > Takes a path to a filename of a Requirements file. Returns a mapping from package name to a [`packaging.requirements.Requirement`](https://packaging.pypa.io/en/latest/requirements/#packaging.requirements.Requirement) object with the following attributes:
  > * `Requirement.name` (`string`): The name of the requirement.
  > * `Requirement.extras` (`set`): A set of extras that the requirement specifies.
  > * `Requirement.specifier` ([`packaging.specifiers.SpecifierSet`](https://packaging.pypa.io/en/latest/specifiers/#packaging.specifiers.SpecifierSet)): A `SpecifierSet` of the version specified by the requirement.
  > * `Requirement.marker` ([`packaging.markers.Marker`](https://packaging.pypa.io/en/latest/markers/#packaging.markers.Marker)): A `Marker` of the marker for the requirement. Can be `None`.
  > Optionally takes an `options` parameter to override the regex used to skip requirements lines.
  > Optionally takes an `include_invalid` parameter to return an `UnparsedRequirement` in the event that a requirement cannot be parsed correctly.

### Available with `pip>=8.0.0`:
* `pip_api.hash(filename, algorithm='sha256')`
  > Returns the resulting as a string.
  > Valid `algorithm` parameters are `'sha256'`, `'sha384'`, and `'sha512'`

### Available with `pip>=19.2`:
* `pip_api.installed_distributions(local=False, paths=[])`
  > As described above, but with an extra optional `paths` parameter to provide a list of locations to look for installed distributions. Attempting to use the `paths` parameter with `pip<19.2` will result in a `PipError`.
