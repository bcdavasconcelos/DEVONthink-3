# Outline mode


## Dark

```css

details {
	margin: 1.5em 10px;
	padding: 0.5em 30px;
	position: relative;
	border-left: 0.2px solid #5A5B5B;
}

details:before {
	color: #B2D2AC;
	font-size: 4em;
	line-height: 0.1em;
	margin-right: 0.25em;
	vertical-align: -0.4em;
}
details p {
	color: #F6F6EE;
	margin-top: 0.5em;
	margin-bottom: 0.5em;
	display: inline;
}
summary {
    cursor: pointer;
    color: #646364;
/* 	background: #FBFAFA; */
 	font-weight: bold;
}
summary:hover {
    color: #d1d0c7;
}
summary:focus { 
    outline: none;
    color: #d1d0c7;
	box-shadow: 0 0 0px;
	border-bottom: 0px solid #5A5B5B;
}
summary::-webkit-details-marker {
    color: none;
    display: none;
    background-color: none;
}
summary:before {
    content: "◦";
    margin-right: 5px;
}
summary.backlinks:before {
    content: "🕸"; 
    font-size: 0.8em;
}


```


## Light

```css

details {
	background: #FBFAFA;
	border-left: 0px solid #E3E4E6;
	margin: 1.5em 10px;
	padding: 0.5em 30px;
	position: relative;
}

details:before {
	color: #BD5D29;
	font-size: 4em;
	line-height: 0.1em;
	margin-right: 0.25em;
	vertical-align: -0.4em;
}
details p {
	color: #23384d;
	margin-top: 0.5em;
	margin-bottom: 0.5em;
	display: inline;
}
summary {
    cursor: pointer;
    color: #E3E4E6;
  	border-bottom: 0px solid #E3E4E6;
/* 	background: #FBFAFA; */
 	font-weight: bold;
}
summary:hover {
    color: #23394D;
}
summary:focus { 
    outline: none;
	box-shadow: 0 0 0px;
}
summary::-webkit-details-marker {
    color: none;
    display: none;
    background-color: none;
}
summary:before {
    content: "◦";
    margin-right: 5px;
}
summary.backlinks:before {
    content: "🕸"; 
    font-size: 0.8em;
}

```
