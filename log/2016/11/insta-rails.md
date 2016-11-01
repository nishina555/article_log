
https://api.instagram.com/oauth/authorize/?client_id=12dcc45d814c4e8d8b7f8d731066ca38&redirect_uri=http://localhost:3000/&response_type=code



curl \-F 'client_id=12dcc45d814c4e8d8b7f8d731066ca38' \
    -F 'client_secret=1c1028ea9d854513842d2cbd62980110' \
    -F 'grant_type=authorization_code' \
    -F 'redirect_uri=http://localhost:3000/' \
    -F 'code=8aa616865b6e4201b90a0cabd6c05041' \https://api.instagram.com/oauth/access_token


https://api.instagram.com/v1/users/self/?access_token=c4eaa6dce92a4827967371e16979cc45

208396677.12dcc45.b4e2772baa9742c2ae4456965b986b64

https://api.instagram.com/v1/users/self/media/recent/?access_token=208396677.12dcc45.b4e2772baa9742c2ae4456965b986b64
