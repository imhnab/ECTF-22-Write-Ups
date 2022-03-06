# Hack the Auth
> This challenge requires : `javascript` and `cookies`
- We have a [link](https://wec-ctf-2022-web.herokuapp.com/q1) with a description as the picture: 

    ![hint](img/hint.png)

- This [link](https://wec-ctf-2022-web.herokuapp.com/q1) link to a login page again :D 
- I tried to `F12` and find a script : 

    ```javascript
        var sha256 = function sha256(ascii) {
            function rightRotate(value, amount) {
                return (value>>>amount) | (value<<(32 - amount));
            };
            
            var mathPow = Math.pow;
            var maxWord = mathPow(2, 32);
            var lengthProperty = 'length'
            var i, j;
            var result = ''
        
            var words = [];
            var asciiBitLength = ascii[lengthProperty]*8;

            var hash = sha256.h = sha256.h || [];
            var k = sha256.k = sha256.k || [];
            var primeCounter = k[lengthProperty];

        
            var isComposite = {};
            for (var candidate = 2; primeCounter < 64; candidate++) {
                if (!isComposite[candidate]) {
                    for (i = 0; i < 313; i += candidate) {
                        isComposite[i] = candidate;
                    }
                    hash[primeCounter] = (mathPow(candidate, .5)*maxWord)|0;
                    k[primeCounter++] = (mathPow(candidate, 1/3)*maxWord)|0;
                }
            }
            
            ascii += '\x80'
            while (ascii[lengthProperty]%64 - 56) ascii += '\x00' 
            for (i = 0; i < ascii[lengthProperty]; i++) {
                j = ascii.charCodeAt(i);
                if (j>>8) return;
                words[i>>2] |= j << ((3 - i)%4)*8;
            }
            words[words[lengthProperty]] = ((asciiBitLength/maxWord)|0);
            words[words[lengthProperty]] = (asciiBitLength)

            for (j = 0; j < words[lengthProperty];) {
                var w = words.slice(j, j += 16);
                var oldHash = hash;
                hash = hash.slice(0, 8);
                
                for (i = 0; i < 64; i++) {
                    var i2 = i + j;

                    var w15 = w[i - 15], w2 = w[i - 2];

                    var a = hash[0], e = hash[4];
                    var temp1 = hash[7]
                        + (rightRotate(e, 6) ^ rightRotate(e, 11) ^ rightRotate(e, 25)) 
                        + ((e&hash[5])^((~e)&hash[6])) 
                        + k[i]
                        + (w[i] = (i < 16) ? w[i] : (
                                w[i - 16]
                                + (rightRotate(w15, 7) ^ rightRotate(w15, 18) ^ (w15>>>3)) 
                                + w[i - 7]
                                + (rightRotate(w2, 17) ^ rightRotate(w2, 19) ^ (w2>>>10)) 
                            )|0
                        );
                    var temp2 = (rightRotate(a, 2) ^ rightRotate(a, 13) ^ rightRotate(a, 22)) 
                        + ((a&hash[1])^(a&hash[2])^(hash[1]&hash[2])); 
                    
                    hash = [(temp1 + temp2)|0].concat(hash); 
                    hash[4] = (hash[4] + temp1)|0;
                }
                
                for (i = 0; i < 8; i++) {
                    hash[i] = (hash[i] + oldHash[i])|0;
                }
            }
            
            for (i = 0; i < 8; i++) {
                for (j = 3; j + 1; j--) {
                    var b = (hash[i]>>(j*8))&255;
                    result += ((b < 16) ? 0 : '') + b.toString(16);
                }
            }
            return result;
        };

        var userAuthentication = function() {
            var username = document.getElementsByName('username')[0].value;
            var password = document.getElementsByName('password')[0].value;
            var hash = sha256(password);
            if(hash === "dfbec338b51c5643ba481625e1075236d3a9a07fbd6393763f253e99024958a4" && username === "admin") {
                window.location = "./q1?username=admin&password=" + password;
            } else {
                alert("Invalid username or password");
            }
        };
    ```

- Okay! We can see the value of username is `admin` and the value of `password` was encrypted by `SHA256` is `dfbec338b51c5643ba481625e1075236d3a9a07fbd6393763f253e99024958a4` --> Decrypting this, result: `Thisismypassword` (This is password)

    ![decryptSHA256](img/DecryptSHA256.png)

- Let login and the page show that :

    ![login](img/login.png)

- Check the `cookies` of this page and see the cookies had the value is `CTF%7BSHA256_AND_WEB%7D` --> We have the Flag.

    ![Cookies](img/Cookies.png)

- Flag : `CTF{BSHA256_AND_WEB}`