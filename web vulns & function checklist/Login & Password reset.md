 - login
	 - [ ] [[Open redirect]] on redirect parameter
	 - [ ] login using `myemail%00@email.com` => if success login as `myemail@email.com` then try [[Account take over (ATO)]] => register `my%00email@email.com`, check username too
	 - [ ] 

- Password reset
	- [ ] [[IDOR]] try to reset other user password, change `id`
	- [ ] header attacks: change `host: evil.com` HTTP header on password reset request, check your mail for `evil.com/resetpassword?token=123`
	- [ ] rate limit=> brute force OPT?
	- [ ] reset password code not tied to email, but it's life time=> lead to [[Account take over (ATO)]]
	      create tow accounts, forget password for one, don't use the code. then forget password for the other and try the first code