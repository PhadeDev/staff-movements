# staff-movements

Power Apps canvas app for Army Cadets branch staff movement tracking.

## Live Site

- GitHub Pages: https://phadedev.github.io/staff-movements/
- Screen copy pages: `docs/screens/`
- Scratch YAML source: `src/`

## Current Build Notes

The screen copy pages are generated with `pa-yaml-wrap` and show a visible payload preview. Always check the preview before pasting into Power Apps Studio.

Valid screen YAML shape:

```yaml
Screens:
  scrDashboard:
    Properties:
      Fill: =RGBA(248,248,248,1)
    Children:
      - recHeader:
          Control: Classic/Button@2.2.0
```

Important traps:

- Do not start screen copy YAML with `- scrName:`. That can cause PA1006 at line 1, column 3.
- Do not put `Control: Screen` under `Screens:`. That causes PA1001 at line 3, column 5: `Property 'Control' not found on type ... ScreenInstance`.
- Child controls still need normal `Control:` values.
- `|-` block scalars are only safe on event handlers and `Items`.
- Single-line Power Fx formulas containing YAML-significant `: ` must be quoted for source validation, e.g. `Default: '={Value: "New to MoD/Branch"}'`.

## Validation

Before pushing:

```bash
python3 - <<'PY'
import yaml, re, json
from pathlib import Path
for path in sorted(Path('src').glob('scr*.yaml')):
    data = yaml.safe_load(path.read_text())
    assert set(data) == {'Screens'}
    screen = next(iter(data['Screens'].values()))
    assert 'Control' not in screen
for path in sorted(Path('docs/screens').glob('scr*.html')):
    text = path.read_text()
    payload = json.loads(re.search(r'const yaml = (.*?);\n', text).group(1))
    assert payload.startswith('Screens:')
    assert 'Control: Screen' not in payload
    yaml.safe_load(payload)
print('OK')
PY
```
