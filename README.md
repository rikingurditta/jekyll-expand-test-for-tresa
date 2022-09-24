# jekyll expand test for tresa

$$
\newcommand{\Tresa}{\mathcal{TRESA}}
$$

testing [this thing](https://jekyllcodex.org/without-plugin/text-expand/#) for tresa leblanc-doucet math grad student at uoft doxxing her publicly

[expand]

text hi ummmmm hiii

![hi](https://cdn.discordapp.com/attachments/712051325944463451/1022333027185074300/unknown.png)

$$
S(x) = \int_0^T \left( \frac{1}{2} \dot x^T M \dot x + \Psi(x) + x^T \left( f_e + f_d \right) \right) dt
$$

[/expand]


here's the code for above:

```markdown
[expand]

text hi ummmmm hiii

![hi](https://cdn.discordapp.com/attachments/712051325944463451/1022333027185074300/unknown.png)

$$
S(x) = \int_0^T \left( \frac{1}{2} \dot x^T M \dot x + \Psi(x) + x^T \left( f_e + f_d \right) \right) dt
$$

[/expand]
```

you can also do it without that include using [\<details\>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details)

<details>
	<summary>hi funny guy click here to read more i promise no more funny pictures</summary>
	<p>
	some mroe text :shy:
	<br>
	<img src=https://cdn.discordapp.com/attachments/991193607115657337/1016860874469810186/20220906_182238.jpg>
	<br>
	$$
	e^{i\pi} + 1 = 0
	$$
	<br>
	i defined a `\Tresa` command so i could write $\Tresa \Tresa \Tresa \Tresa \Tresa$ without typing `\mathcal{Tresa}` 7 times
	</p>
</details>