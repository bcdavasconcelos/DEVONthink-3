# SanHo for Obsidian

```applescript
/* "Cormorant Garamond SemiBold" */
body {
font-family: "Alegreya";
-ms-text-size-adjust: 100%;
-webkit-text-size-adjust: 100% margin: 0;
width: auto;
}

.theme-dark {
/* 
    --background-primary: #2b3636;
    --background-primary-alt: #436576;
    --background-secondary: #2b3636;
    --background-secondary-alt: #2b3636;
 */
     --background-primary: #1e212d;
    --background-primary-alt: #171a23;
    --background-secondary: #1D1F28;
    --background-secondary-alt: #1e212d;
    --background-accent: #2b3636;
    --background-modifier-border: #436576;
    --background-modifier-form-field: rgba(0, 0, 0, 0.3);
    --background-modifier-form-field-highlighted: rgba(0, 0, 0, 0.22);
    --background-modifier-box-shadow: rgba(0, 0, 0, 0.3);
    --background-modifier-success: #197300;
    --background-modifier-error: #3d0000;
    --background-modifier-error-hover: #470000;
    --background-modifier-cover: rgba(0, 0, 0, 0.8);
    --text-accent: #9bcb98;
    --text-accent-hover: #197300;
    --text-normal: #dcddde;
    --text-muted: #888;
    --text-faint: #555;
    --text-error: #800000;
    --text-error-hover: #990000;
    --text-highlight-bg: rgba(255, 255, 0, 0.4);
    --interactive-normal: #2b3636;
    --interactive-hover: #2b3636;
    --interactive-accent: #9bcb98;
	--interactive-accent-rgb: #a0e85b;
    --interactive-accent-hover: #a0e85b;
    --scrollbar-active-thumb-bg: rgba(255, 255, 255, 0.2);
    --scrollbar-bg: rgba(255, 255, 255, 0.05);
    --scrollbar-thumb-bg: rgba(255, 255, 255, 0.1);
}

.* {
background-color: #2b3636;
color: #cdcccc;
text-rendering: optimizeLegibility;
}

.html {
background-color: #2b3636;
color: #cdcccc;
text-rendering: optimizeLegibility;
}


/* 
body {
font-family: "Cormorant Garamond SemiBold", "GFS DidotClassic";
-ms-text-size-adjust: 100%;
-webkit-text-size-adjust: 100% margin: 0;
padding: 50px 2rem 2rem;
max-width: 800px;
}
 */

article,
aside,
details,
figcaption,
figure,
footer,
header,
hgroup,
main,
menu,
nav,
section,
summary {
display: block
}

audio,
canvas,
progress,
video {
display: inline-block;
vertical-align: baseline
}

audio:not([controls]) {
display: none;
height: 0
}

[hidden],
template {
display: none
}

a {
background-color: transparent
}

a:active,
a:hover {
outline: 0
}

abbr[title] {
border-bottom: 1px dotted
}

b,
strong {
font-weight: bold;
}

em {
font-style: italic;
}

strong em, 
em strong {
font-weight: bold;
font-style: italic;
}

dfn {
font-style: italic
}

small {
font-size: 80%
}

sub,
sup {
font-size: 0.6em;
/* color: #666; */
    font-family: Souce Sans Pro;
position: middle;
line-height: 0;
vertical-align: super;
}

sup {
top: -0.5em
}

sub {
bottom: -0.25em
}

svg:not(:root) {
overflow: hidden
}

figure {
display: inline-block;
width: 100%;
margin: 1em 0;
text-align: center;
}

figcaption {
font-style: italic;
text-align: center;
background: transparent;
color: #eee;
}

/* 
img {
border: 0;
max-width: 100%;
margin-top: 2em;
margin-bottom: 2em;
height: auto;
}
 */
 img{
	display: block;
	margin-left: auto;
	margin-right: auto;
	width: 80%;
	max-width: 100%;
	height: auto;
	border: 1px solid #ddd;
	border-radius: 4px;
	padding: 5px;
}
img:hover {
  box-shadow: 0 0 2px 1px rgba(0, 140, 186, 0.5);
}
hr {
-moz-box-sizing: content-box;
box-sizing: content-box;
height: 0;
border: none;
border-top-style: dashed;
border-top-color: #797878;
border-top-width: 2px;
background: none;
width: 70%;
margin: 2em auto;
}

pre {
overflow: auto
}


button,
input,
optgroup,
select,
textarea {
color: inherit;
font: inherit;
margin: 0
}

button {
overflow: visible
}

button,
select {
text-transform: none
}

button::-moz-focus-inner,
input::-moz-focus-inner {
border: 0;
padding: 0
}

input {
line-height: normal
}

input[type="checkbox"],
input[type="radio"] {
box-sizing: border-box;
padding: 0
}

input[type="search"] {
-webkit-appearance: textfield;
-moz-box-sizing: content-box;
-webkit-box-sizing: content-box;
box-sizing: content-box
}

fieldset {
border: 1px solid #c0c0c0;
margin: 0 2px;
padding: 0.35em 0.625em 0.75em
}

legend {
border: 0;
padding: 0
}

textarea {
overflow: auto
}

optgroup {
font-weight: bold
}

table {
border: 1px solid #f3ecd8;
border-collapse: collapse;
display: table;
empty-cells: hide;
margin: 1px auto 1.3125em auto;
padding: 0;
table-layout: fixed;
width: 100%;
}

caption {
display: table-caption;
font-weight: 700;
}

col {
display: table-column;
}

colgroup {
display: table-column-group;
}

tbody {
display: table-row-group;
}

tfoot {
display: table-footer-group;
}

thead {
display: table-header-group;
}

td,
th {
display: table-cell;
}

tr {
display: table-row;
}

table th,
table td {
font-size: 1em;
line-height: 1.4;
padding: .5em 1em 0;
border: 1px solid #f3ecd8;
}

table thead {
background: #f3ecd8;
/* color: #666; */
text-transform: uppercase;
}


/* table tbody {
  background: rgba(239, 231, 218, .3);
} */

table tfoot {
background: rgba(220, 215, 187, .5);
margin-bottom: 0.5em;
}

table th+th,
table td+td,
table th+td {
border-left: 1px solid #ddd;
}

table thead tr:first-child th:first-child,
table tbody tr:first-child td:first-child {
-webkit-border-radius: 4px 0 0 0;
-moz-border-radius: 4px 0 0 0;
border-radius: 4px 0 0 0
}

table thead tr:first-child th:last-child {
-webkit-border-radius: 0 4px 0 0;
-moz-border-radius: 0 4px 0 0;
border-radius: 0 4px 0 0
}

table tr:nth-child(odd),
table th:nth-child(odd),
table td:nth-child(odd) {
background: rgba(255, 255, 255, 0.1)
}

table tr:nth-child(even),
table td:nth-child(even) {
background: rgba(177, 169, 158, 0.08)
}

table tbody tr:first-child td:last-child {
-webkit-border-radius: 0 4px 0 0;
-moz-border-radius: 0 4px 0 0;
border-radius: 0 4px 0 0
}

table tbody tr:last-child td:first-child {
-webkit-border-radius: 0 0 0 4px;
-moz-border-radius: 0 0 0 4px;
border-radius: 0 0 0 4px
}

table tbody tr:last-child td:last-child {
-webkit-border-radius: 0 0 4px 0;
-moz-border-radius: 0 0 4px 0;
border-radius: 0 0 4px 0
}

table tbody tr:nth-child(odd) {
background-color: rgba(38, 116, 152, 0.02)
}

*,
*:before,
*:after {
-webkit-box-sizing: border-box;
-moz-box-sizing: border-box;
box-sizing: border-box
}

h1,
h2,
h3 {
font-family: inherit;
}

h4,
h5,
h6 {
font-family: inherit;
font-style: italic;
}

html,
body {
width: 100%;
}


/** Vertical Rhythm **/

html {
font-size: 20px;
line-height: 24px;
}

@media screen and (max-height:700px) {
html {
font-size: 18px;
line-height: 24px;
}
}

@media screen and (max-width:640px) {
html {
font-size: 18px;
line-height: 24px;
}
}

body {
font-size: 1em;
line-height: 1.8em;
text-align: justify;
text-justify: auto;
margin: auto;
}

h1,
h2,
h3 {
word-break: break-word;
}

h1 {
font-size: 1.5em;
}

h1:before {
    content: "# ";
color: #cf574a;
font-size: 1.3em;
}

h2 {
font-size: 1.25em;
}

h2:before {
    content: "## ";
color: #cf574a;
font-size: 1.0em;
}

h3 {
font-size: 1.1em;
}
h3:before {
color: #cf574a;
   content: "### ";
   font-size: 0.9em;
}
h4 {
font-size: 1.0em;
}
h4:before {
color: #cf574a;
font-size: 0.7em;
   content: "#### ";
}
h5,
h6 {
font-size: 1em;
}
h5:before {
    content: "##### ";
color: #cf574a;
font-size: 0.5em;
}
h6:before {
    content: "###### ";
color: #cf574a;
font-size: 0.5em;
}

p,
ul,
ol,
pre,
table,
blockquote {
margin-top: 0em;
margin-bottom: 1em;
}

ul ul,
ol ol,
ul ol,
ol ul {
margin-top: 0em;
margin-bottom: 0em;
}

.footnotes {
font-family: "Alegreya";
font-size: 0.8em;
line-height: 1.4em;
}

.footnotes ol li p{
margin-top: 1em;
margin-right: 0.5em;
margin-left: 0.5em;
margin-bottom: 1.0em;
}

.footnotes ol li ul li{
margin-top: 0em;
margin-right: 0.5em;
margin-left: 0.5em;
margin-bottom: 0em;
}


/** End Vertical Rhythm **/

h1,
h2,
h3,
h4,
h5,
h6,
h1 strong,
h2 strong,
h3 strong,
h4 strong,
h5 strong,
h6 strong {
color: inherit;
line-height: 2.0em;
margin-top: 2.0em;
margin-bottom: 1em;
}

h4,
h5,
h6,
strong {
font-weight: bold;
}

strong {
font-weight: bold;
}

em {
font-style: italic;
}

strong em,
em strong {
font-style: italic;
font-weight: bold;
}

small {
color: rgba(26, 25, 25, 0.55)
}

a,
input,
textarea {
transition: color .1s ease-out, border-color .1s ease-out, background-color .1s ease-out, background-image .1s ease-out
}

a {
  text-decoration: none;
/*   color: #cdcccc; */
color: #9bcb98;
/*   font-weight: 500; */
}
a:hover, a:active, a:focus {
  outline: 0;
  color: #2b3636;
  background: #9bcb98;
}

ul,
ol {
list-style-position: outside;
margin-top: 0;
padding-left: 2em
}

ul {
list-style-type: disc
}

dt {
font-family: inherit;
}

blockquote {
background: #556c68;
border-left: 10px solid #2d3e42;
margin: 1.5em 10px;
padding: 0.5em 10px;
position: relative;
quotes: "\201C""\201D""\2018""\2019";
}

blockquote:before {
color: #2d3e42;
content: open-quote;
font-size: 4em;
line-height: 0.1em;
margin-right: 0.25em;
vertical-align: -0.4em;
}

blockquote p {
color: #cdcccc;
margin-top: 0.5em;
margin-bottom: 0.5em;
display: inline;
}

blockquote figcaption {
color: #cdcccc;
}

q::before {
content: "\201C"
}

q::after {
content: "\201D"
}

kbd,
pre,
samp {
font-family: monospace, monospace;
font-size: 1em;
}

code {
font-family: Ubunto, monospace;
font-size: .85rem;
color: #cdcccc;
width: auto;
margin-top: 0em;
margin-bottom: 0em;
padding: 0px 0rem;
background-color: #556c68;
border-radius: 0rem;
border: 0px solid #d6d8db;
/* border-left: 10px solid #f3ecd8; */
white-space: pre-wrap;
overflow-wrap: break-word
}

.citations ol li p{
    line-height: 1em;
}

pre code {
display: block;
padding: .625rem 1rem
}

input,
textarea {
padding: .5rem .625rem;
border-radius: .25rem;
border: 1px solid #ccdae3;
background-color: #2b3636;
-webkit-appearance: none
}

input:focus,
textarea:focus {
border-color: #a0abb2;
outline: 0
}

textarea {
resize: vertical
}

input+input,
label+input {
margin-left: 1.25rem
}

label {
font-size: 1.1875rem
}

.rtl {
direction: rtl
}

*::selection {
color: #fff;
background: #e0516a
}

*::-moz-selection {
color: #fff;
background: #e0516a
}

*::selection:window-inactive {
background: #e0516a
}

@media print {
body {
padding: 20px;
}
}


/*
  CriticMarkup CSS by Fletcher T. Penney

  Shows CSS tricks to improve visibility of CriticMarkup output
*/

del,
ins,
mark,
span.critic {
/* color: #fffdfa!important; */
}

del {
background: rgba(224, 81, 106, .35);
}

ins {
background: #67b03e;
}

mark,
span.critic {
background: #337e47;
color: #cdcccc;
}

span.critic.comment,
span.criticcomment {
background: #fb6961;
}

span.critic.comment::before {
content:"{>> ";
}

span.critic.comment::after {
content: " <<}";
}

```
