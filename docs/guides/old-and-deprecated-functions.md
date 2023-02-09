# Old, deprecated, and removed functions

## Removed from the manual

Some functions where introduced years ago because they made sense
at their time. However, LaTeX has evolved and currently they are of
limited interest, and therefore their description has been moved here,
for not to clutter the manual.

`strings` • Package option with a value. Selects the encoding of
strings in languages supporting this feature. Predefined labels are
`generic` (for traditional TeX, LICR and ASCII strings), `unicode` (for
engines like `xetex` and `luatex`) and `encoded` (for special cases
requiring mixed encodings). Other allowed values are font encoding
codes (`T1`, `T2A`, `LGR`, `L7X`...), but only in languages supporting
them. Be aware with `encoded` captions are protected, but they work in
`\MakeUppercase` and the like (this feature misuses some internal LaTeX
tools, so use it only as a last resort).

`mapfont=direction` • Option in `\babelprovide`. Assigns the font for
the writing direction of this language (only with `bidi=basic`).
Instead of this option use `onchar`, based on the script. More
precisely, what `mapfont=direction` means is, ‘when a character has the
same direction as the script for the “provided” language, then change
its font to that set for this language’. There are 3 directions,
following the bidi Unicode algorithm, namely, Arabic-like, Hebrew-like
and left to right. So, there should be at most 3 directives of this
kind. The only value is `direction`, and this option won’t be developed
any further.

## Fonts

A couple of tentative macros were provided by `babel` <3.9g with a
partial solution for “Unicode” fonts. Shortly after, with the
introduction of `\babelfont`, they were deprecated, and now, finally,
have been removed. A short description follows, for reference:
* `\babelFSstore{babel-language}` sets the current three   basic families (rm, sf, tt) as the default for the language given.
* `\babelFSdefault{babel-language}{fontspec-features}` patches `\fontspec` so that the given features are always passed as   the optional argument or added to it (not an ideal solution).
```tex
\setmainfont[Language=Turkish]{Minion Pro}
\babelFSstore{turkish}
\setmainfont{Minion Pro}
\babelFSfeatures{turkish}{Language=Turkish}
```
And the code, in case you need it for some reason:
{% raw %}
```tex
\newcommand\babelFSstore[2][]{%
  \bbl@ifblank{#1}%
    {\bbl@csarg\def{sname@#2}{Latin}}%
    {\bbl@csarg\def{sname@#2}{#1}}%
  \bbl@provide@dirs{#2}%
  \bbl@csarg\ifnum{wdir@#2}>\z@
    \let\bbl@beforeforeign\leavevmode
    \EnableBabelHook{babel-bidi}%
  \fi
  \bbl@foreach{#2}{%
    \bbl@FSstore{##1}{rm}\rmdefault\bbl@save@rmdefault
    \bbl@FSstore{##1}{sf}\sfdefault\bbl@save@sfdefault
    \bbl@FSstore{##1}{tt}\ttdefault\bbl@save@ttdefault}}
\def\bbl@FSstore#1#2#3#4{%
  \bbl@csarg\edef{#2default#1}{#3}%
  \expandafter\addto\csname extras#1\endcsname{%
    \let#4#3%
    \ifx#3\f@family
      \edef#3{\csname bbl@#2default#1\endcsname}%
      \fontfamily{#3}\selectfont
    \else
      \edef#3{\csname bbl@#2default#1\endcsname}%
    \fi}%
  \expandafter\addto\csname noextras#1\endcsname{%
    \ifx#3\f@family
      \fontfamily{#4}\selectfont
    \fi
    \let#3#4}}
\let\bbl@langfeatures\@empty
\def\babelFSfeatures{% make sure \fontspec is redefined once
  \let\bbl@ori@fontspec\fontspec
  \renewcommand\fontspec[1][]{%
    \bbl@ori@fontspec[\bbl@langfeatures##1]}
  \let\babelFSfeatures\bbl@FSfeatures
  \babelFSfeatures}
\def\bbl@FSfeatures#1#2{%
  \expandafter\addto\csname extras#1\endcsname{%
    \babel@save\bbl@langfeatures
    \edef\bbl@langfeatures{#2,}}}
```
{% endraw %}