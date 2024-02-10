# kiwi.com

### Auth:

l’autenticazione tramite kiwi avviene o tramite social come `facebook` e `google`

Il processo è il seguente :
dopo aver inserito la mail viene mandata una richiesta post a `https://auth.skypicker.com/v1/user.exists` con la mail e il sorgente di input

```bash
curl 'https://auth.skypicker.com/v1/user.exists' \
  -H 'authority: auth.skypicker.com' \
  -H 'accept: application/json' \
  -H 'accept-language: en-US,en;q=0.7' \
  -H 'authorization: Basic NTQzM2VjY2NhZmY2Nzo=' \
  -H 'content-type: application/json' \
  -H 'origin: https://www.kiwi.com' \
  -H 'referer: https://www.kiwi.com/' \
  -H 'sec-ch-ua: "Not_A Brand";v="8", "Chromium";v="120", "Brave";v="120"' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'sec-ch-ua-platform: "Linux"' \
  -H 'sec-fetch-dest: empty' \
  -H 'sec-fetch-mode: cors' \
  -H 'sec-fetch-site: cross-site' \
  -H 'sec-gpc: 1' \
  -H 'user-agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36' \
  --data-raw '{"email":"ahekal369@gmail.com","brand":"kiwicom"}' \
  --compressed
```

nel caso in cui l'account essite effetivamnte risponde con:

```bash
{"exists":true,"passwordless":true,"verified":true,"social":{"facebook":false,"google":true}}
```
dove nella risposta si capice se è collegato a qualche social e se è richiesto l'inserimento della password 

se l'account ad esempio è connesso a un servizio di google e `passwordless` è true allora la ui manda una richiesta per inviare con codice di verifica a `/v1/user.otpLogin` con gli stessi dati 

```bash
curl 'https://auth.skypicker.com/v1/user.otpLogin' \
  -H 'authority: auth.skypicker.com' \
  -H 'accept: application/json' \
  -H 'accept-language: en-US,en;q=0.7' \
  -H 'authorization: Basic NTQzM2VjY2NhZmY2Nzo=' \
  -H 'content-type: application/json' \
  -H 'origin: https://www.kiwi.com' \
  -H 'referer: https://www.kiwi.com/' \
  -H 'sec-ch-ua: "Not_A Brand";v="8", "Chromium";v="120", "Brave";v="120"' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'sec-ch-ua-platform: "Linux"' \
  -H 'sec-fetch-dest: empty' \
  -H 'sec-fetch-mode: cors' \
  -H 'sec-fetch-site: cross-site' \
  -H 'sec-gpc: 1' \
  -H 'user-agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36' \
  --data-raw '{"email":"ahekal369@gmail.com","brand":"kiwicom"}' \
  --compressed
```

la Risposta è : 
```json
{
  "transaction_id": "gAAAAABlx9xWqN7f8mt5H4TTJsEclrJ6ePczK_jxThZOeEjyNEeubPV_x0irlQ55gBI2uaosfuWyXLla_OeUStRZtv5UM7NJQLNEF9UjLS3_K-YPhaPo6Fk="
}

```
questo `transaction_id` dovra essere inviato in seguito con il codice di verifica per completare il loggin COME SI VEDE NELLA PROSSIMA RICHIESTA.

per completare il login allora si fa un altra richiesta che contiene il `transaction_id` e il `passcode` recivuti via email per avere indietro sia il 'token' di login che l'user_id


```bash
curl 'https://auth.skypicker.com/v1/user.otpLogin' \
  -H 'authority: auth.skypicker.com' \
  -H 'accept: application/json' \
  -H 'accept-language: en-US,en;q=0.7' \
  -H 'authorization: Basic NTQzM2VjY2NhZmY2Nzo=' \
  -H 'content-type: application/json' \
  -H 'origin: https://www.kiwi.com' \
  -H 'referer: https://www.kiwi.com/' \
  -H 'sec-ch-ua: "Not_A Brand";v="8", "Chromium";v="120", "Brave";v="120"' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'sec-ch-ua-platform: "Linux"' \
  -H 'sec-fetch-dest: empty' \
  -H 'sec-fetch-mode: cors' \
  -H 'sec-fetch-site: cross-site' \
  -H 'sec-gpc: 1' \
  -H 'user-agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36' \
  --data-raw '{"transaction_id":"gAAAAABlx9xWqN7f8mt5H4TTJsEclrJ6ePczK_jxThZOeEjyNEeubPV_x0irlQ55gBI2uaosfuWyXLla_OeUStRZtv5UM7NJQLNEF9UjLS3_K-YPhaPo6Fk=","passcode":"10990095"}' \
  --compressed
```

che risponde con : 

```json
{
    "token":"eyJhbGciOiJIUzI1...............",
    "user_id":"............"
}
```



