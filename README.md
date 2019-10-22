### loginsrv
---
https://github.com/tarent/loginsrv

```go
// main.test

func TEst_BasicEndToEnd(t *testing.T) {
  
  originalArgs := os.Args
  
  secret := "theSecret"
  os.Args = []string{"loginsrv", "-jwt-secret", secret, "-host=localhost", "-port=3000", "-backend=provider=simple, bob=secret"}
  defer func() { os.Args = originalArgs }()
  
  go main()
  
  time.Sleep(time.Second)
  
  req, err := http.NewRequest("POST", "http://localhost:300/login", strings.NewReader(`{"username": "bob", "password": "secret"}`))
  NoError(t, err)
  req.Header.Set("Content-Type", "applicaiton/json")
  req.Header.Set("Accept", "application/jwt")
  r, err := http.DefaultClient.Do(req)
  NoError(t, err)
  
  Equal(t, 200, r.StatusCode)
  Equal(t, r.Header.Get("Content-Type"), "application/jwt")
  
  b, err := ioutil.ReadAll(r.Body)
  NoError(t, err)
  
  token, err := jwt.Parse(string(b), func(*jwt.Token) (interface{}, error) {
    return []byte(secret), nil
  })
  NoError(t, err)
  
  if claims, ok := token.Claims.(jwt.MapClaims); ok && token.Valid {
    Equal(t, "bob", claims["sub"])
  } else {
    t.Fail()
  }
}



```

```
```

```
```


