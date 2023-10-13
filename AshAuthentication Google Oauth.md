```
<@177904715001102336> Hey, I found a working implementaiton in one of our apps. Just keep in mind that this is not using regular google oauth but like a company specific oauth. Hoping it might help you track down your issue though

```elixir
oauth2 :google do
  client_id(YourApp.AuthSecrets)
  client_secret(YourApp.AuthSecrets)
  redirect_uri(YourApp.AuthSecrets)
  site(YourApp.AuthSecrets)

  authorize_url("https://accounts.google.com/o/oauth2/auth")
  token_url("https://accounts.google.com/o/oauth2/token")
  user_url("https://www.googleapis.com/oauth2/v1/userinfo")

  authorization_params(
    scope: "https://www.googleapis.com/auth/userinfo.email https://www.googleapis.com/auth/userinfo.profile",
    prompt: "select_account"
  )
end
```
```