# Simple Command Line Function to Decode JWTs

I come across a lot of [JSON Web Tokens (JWTs)](https://en.wikipedia.org/wiki/JSON_Web_Token) these days, and I often want a quick way to see what information is encoded in them. 

An easy solution is to visit [jwt.io](https://jwt.io/), which has an interactive JWT decoder. However, I don’t love this solution for a few reasons:

Some of these JWTs contain sensitive information. I don’t want to paste them into a website where they can be leaked (accidentally or otherwise).
It can be cumbersome to visit a webpage, scroll down, and then paste into a text box, especially as I tend to be very command line focused.

In short, I wanted an easy way to decode a JWT locally from the command line. I have been a longtime fan of jq, and figured it could do this. I don’t generally care about validating the signature. I just want to see the contents.

After some searching and reading the docs, I wrote a simple function which I added to my ~/.zshrc:

```
jwt-decode() {
  jq -R 'split(".") |.[0:2] | map(@base64d) | map(fromjson)' <<< $1
}
```

I use it like this:

```
% jwt-decode eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
[
  {
    "alg": "HS256",
    "typ": "JWT"
  },
  {
    "sub": "1234567890",
    "name": "John Doe",
    "iat": 1516239022
  }
]
```

The only dependency is jq, which is easy to install (e.g. brew install jq on a Mac). This command splits on ., base64 decodes each piece, parses them as JSON, and then pretty prints.
