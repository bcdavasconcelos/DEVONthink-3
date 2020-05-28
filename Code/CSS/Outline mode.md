# Outline mode


## Dark

```css

details {
	background: #3B3B3C;
	border-left: 2px solid #5A5B5B;
	margin: 1.5em 10px;
	padding: 0.5em 30px;
	position: relative;
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
  	border-bottom: 2px solid #5A5B5B;
/* 	background: #FBFAFA; */
 	font-weight: bold;
}
summary:hover {
    color: #d1d0c7;
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


## Light 

```css

details {
	padding-left: 20px;
	padding-right: 20px;
	border-left: 2px solid #D6D6D6;
	margin: 1.5em 10px;
	padding: 0.5em 30px;
	position: relative;
    background-color: #EBEBEB;
 	max-width: 1200px; 
}
summary {
    cursor: pointer;
/* 
    color: #E3E4E6;
    background-color: #FEFEFE;
 */
 	font-weight: bold;
 	color: #646364;
  	border-bottom: 2px solid #D6D6D6;
}
summary:hover {
    color: #23394D;
/*     background-color: #FBF2F2; */
}
summary:focus { 
    outline: none;
	box-shadow: 0 0 0px;
}
summary::-webkit-details-marker {
    color: none;
    background-color: none;
}
summary::-webkit-details-marker {
    display: none;
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
