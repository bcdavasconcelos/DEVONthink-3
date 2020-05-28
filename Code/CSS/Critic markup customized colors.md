# Critic markup customized colors

```css

/*
  CriticMarkup CSS by Fletcher T. Penney

  Shows CSS tricks to improve visibility of CriticMarkup output
*/

del,
ins,
mark,
span.critic {
/* 	color: #fffdfa!important; */
}

del {
	background: rgba(224, 81, 106, .35);
}

ins {
	background: #67b03e;
}

mark,
span.critic {
	background: #fec03e;
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
