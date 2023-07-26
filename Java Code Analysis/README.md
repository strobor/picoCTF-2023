# Java Code Analysis!?!

## Challenge

BookShelf Pico, my premium online book-reading service.

I believe that my website is super secure. I challenge you to prove me wrong by reading the 'Flag' book!

Here are the credentials to get you started:

- Username: "user"
- Password: "user"

Source code can be downloaded [here](https://artifacts.picoctf.net/c/480/bookshelf-pico.zip).

## Solution

Logging into the website and attempting to view the Flag book, we are told that we need to the have Admin role to access this book.

We search the source files for "secret" and find `SecretGenerator.java`.

![](./image0.png)

We look for where `getServerSecret` is used, and find `JwtService.java`.

![](./image1.png)

Because we know the server secret, which is just "1234", we can generate our own JWT (JSON Web Tokens). So, we can give our account the Admin role.

We view the Application section of Chrome DevTools on the book website. We see our current token claims and signed token.

![](./image2.png)

Using these values and the strings from `JwtService` we create a payload using an [online JWT builder](http://jwtbuilder.jamiekurtz.com/) and modify the role to "Admin".

![](./image3.png)

We input the Key as `1234` and use the `HS256` algorithm, matching what is used in the source, then create the signed JWT.

![](./image4.png)

We copy these values into the `token-payload` and `auth-token` fields respectively:

```
{
    "iss": "bookshelf",
    "iat": 1672560000,
    "exp": 1704096000,
    "aud": "",
    "sub": "",
    "userId": 1,
    "email": "user",
    "role": "Admin"
}
```

(We need to remove the quotes around the 1 for `userId` to match the original data.)

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJib29rc2hlbGYiLCJpYXQiOjE2NzI1NjAwMDAsImV4cCI6MTcwNDA5NjAwMCwiYXVkIjoiIiwic3ViIjoiIiwidXNlcklkIjoiMSIsImVtYWlsIjoidXNlciIsInJvbGUiOiJBZG1pbiJ9.MfjNYbL0XrvgBrLV5ROeoTf9HO9NN45KcW-ctdxa3f8
```

If we try now to access the Flag book, we get an error loading the PDF. However, we can now access an Admin Dashboard by clicking on our profile picture. From there, we can change the role of our User account to Admin. Then, we log out, log back in, and view the book to get the flag.

`picoCTF{w34k_jwt_n0t_g00d_7745dc02}`
