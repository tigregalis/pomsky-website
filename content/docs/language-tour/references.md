---
title: 'References'
description: 'Matching the same thing more than once'
excerpt: ''
date: 2022-05-17T13:55:00+00:00
lastmod: 2022-05-17T13:55:00+00:00
draft: false
images: []
menu:
  docs:
    parent: 'language-tour'
weight: 209
toc: true
---

Sometimes it's useful to match the same text as we matched before. For example, to match strings
in single or double quotes, we can write

```pomsky
:(['"' "'"]) !['"' "'"]* ::1
```

This consists of three parts: First, there's a capturing group matching a quote. We then match an
arbitrary number of characters that aren't quotes. Finally, there's a {{<po>}}::1{{</po>}}
reference. This matches the same text as was captured in capturing group number 1. In other words,
if the string started with {{<po>}}"{{</po>}}, it also has to end with
{{<po>}}"{{</po>}}, and if it started with {{<po>}}'{{</po>}}, it has to end with
{{<po>}}'{{</po>}}.

Another application is XML tags:

```pomsky
'<' :([word]+) '>' !['<']* '</' ::1 '>'
```

This is by no means a complete XML parser, but it recognizes an XML tag (without attributes) that
doesn't contain other XML tags. For example, it correctly matches `<span>Hello world</span>`. With a
backreference, it ensures that the closing tag is the same as the opening tag.

Pomsky has three kinds of references:

- **Numeric references**, e.g. {{<po>}}::3{{</po>}}, match a capturing group by its number.
- **Named references**, e.g. {{<po>}}::name{{</po>}}, match a named capturing group by its
  name.
- **Relative references**, e.g. {{<po>}}::-1{{</po>}} or {{<po>}}::+2{{</po>}}, match a
  capturing group relative to the current position. For example, {{<po>}}::-1{{</po>}}
  matches the previous capturing group, {{<po>}}::+1{{</po>}} matches the next one.

Note that some regex engines only support backreferences, not forward references. And even when
forward references are supported, the referenced group must have been already matched. I.e., this
is not allowed:

```pomsky
# doesn't work!
::1 :('test')
```

However, forward references can be used in repetitions to match what the referenced group captured
in the previous repetition:

```pomsky
(::forward | :forward('test')  '!')*
```

This matches the text `test!test`, for example. In the first repetition, the second alternative
matches `test!`, and the text `test` is captured by the `forward` capturing group. In the second
iteration, the forward reference matches the text `test`.
