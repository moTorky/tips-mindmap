 - login
	 - [ ] [[Open redirect]] on redirect parameter
	 - [ ] login using `myemail%00@email.com` => if success login as `myemail@email.com` then try ATO => register `my%00email@email.com`, check username too
	 - [ ] 

- Password reset
	- [ ] [[IDOR]] try to reset other user password, change `id`
	- [ ] header attacks: change `host: evil.com` HTTP header on password reset request, check your mail for `evil.com/resetpassword?token=123`
	- [ ] rate limit=> brute force OPT?