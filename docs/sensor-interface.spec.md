# The sensor interface

A sensor emits a JSON array of **findings**. Each finding names one smell and lists where it occurs. `habit-sensors` concatenates these arrays, and `habit-mapper` consumes them. This document specifies what a sensor must produce.

## Finding structure

A finding is a JSON object with four fields:

| Field | Required | Meaning |
|-------|----------|---------|
| `smell` | Yes | Canonical smell name from [smell-vocabulary.md](smell-vocabulary.md). The mapper routes fixes by this value. |
| `language` | No | Programming language. When present, the mapper prefers a language-specific guide. |
| `details` | Yes | Object of facts about the smell itself (e.g., thresholds). Shape depends on the smell. |
| `issues` | Yes | Array of occurrence objects. |

### Issue object

Each element in `issues` has two fields:

| Field | Required | Meaning |
|-------|----------|---------|
| `key` | Yes | Identifies the occurrence. Usually a file path, but can be any string (e.g., a module name). Used for snoozing. |
| `details` | Yes | Object with location and context. Conventional fields: |

Conventional fields in issue `details`:

| Field | Meaning |
|-------|---------|
| `file` | File path where the smell was found |
| `line` | Line number |
| `column` | Column number |
| `content` | Code snippet (e.g., function signature) |
| `message` | Human-readable explanation from the tool |
| `source` | Tool and rule that detected the smell (e.g., `ruff:PLR0913`) |

## Path handling

Sensors report paths in whatever form their tool produces. The runner normalizes all paths to be **relative to the project root** using forward slashes.

Rules:
- Paths are anchored lexically; existence is not checked.
- Non-path keys (e.g., module names) are passed through unchanged.
- A path that cannot be anchored to the project directory causes a sensor failure.
- A key that refers to multiple different files causes a sensor failure.
- Malformed output (e.g., `details` not an object) causes a sensor failure.

Failures are reported as standard sensor errors (stderr message + exit code 1), and the sensor's findings are dropped.

## Output format

A sensor's command outputs a JSON array of findings.

- A clean run outputs `[]`.
- Findings are concatenated across sensors by `habit-sensors`.

For complete examples and configuration details, see [habit-sensors.spec.md](habit-sensors.spec.md).