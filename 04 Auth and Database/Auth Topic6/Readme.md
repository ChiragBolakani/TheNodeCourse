# bcrypt

bcrypt is a library which allows the user to hash passwords and secure their application. Using bcrypt we can securely hash the password and then store it in the database which can also be compared later on to verify the user. If your database gets hacked in this case, the hacker will be left with random hash strings instead of plain text. 

To know more about bcrypt you can visit at: [bcrypt](https://github.com/kelektiv/node.bcrypt.js/)

### Generate Hash

In order to hash a password we use the `hash()` function which takes 2 parameters. The first is the data meant to be hashed and the second is the ***salt*** or ***saltrounds***. A salt is an extra layer of security that is added at the beginning of the hash to keep the passwords safe in case of a pre-computed hash attack. The salt is a number which denotes the complexity of the hash. The higher its value the longer time it takes for the password to be hashed.

The `hash()` method also has a callback function (the callback will have parameters like error and result) and it returns a Promise that needs to be handled.

```javascript
bcrypt.hash(myPlaintextPassword, saltRounds, function(err, hash) {
    // This will generate the hash of the myPlaintextPassword
});
```
```javascript
const bcrypt = require('bcrypt');

const pass = "plainText"
const salt = 10

function genHash() {
    bcrypt.hash(pass, salt, (err, hash)=>{
        console.log(hash);
    });
}
```

***Output*** : 

```javascript
$2b$10$bXB8hu1dctSHbOTkOtr2o.y6K6PKOHOuaxUfXVNY1rp4IAjKyX5vu //the hashed password
```

### Storing Hash in MongoDB Database

We can use this hashed password generated by bcrypt and then save it to our MongoDB Database. The plainText password is passed in the `hash()` function and then it can be saved in the Database. 

```javascript
const newUser = new User({
       Email : "email@email.com",
       Password : "plainText"
   })
   
   bcrypt.hash(newUser.password, 10, function(err,hash){
       if(err){
           console.log(err) //to check errors
       }else{
           newUser.Password = hash;
           newUser.save((err,user)=>{
               if(err){
                   console.log(err);
               }else{
                   console.log("saved");
               }
           })
       }
   })
```

### Comparing passwords

bcrypt provides a `compare()` function using which can be used to compare a plainText with a hashed string. It's first parameter is the plainText and the second parameter is the hashed string. 

```javascript
bcrypt.compare(myPlaintextPassword, hash, function(err, result) {
    // result
});
```

This function also takes a callback function which takes in two parameters ***error*** and ***result*** and returns a boolean True or False.

```javascript
const hashedPass =  "$2b$10$bXB8hu1dctSHbOTkOtr2o.y6K6PKOHOuaxUfXVNY1rp4IAjKyX5vu"

function compare(){
    bcrypt.compare("plainText", hashedPass, function(err, compared){
        if(err){
            console.log(err)
        }else{
            console.log(compared)
        }
    })
}
```

***Output*** : 
```javascript
true
```

We can use the `compare()` function and compare the hashed password stored in the MongoDB Database and compare it with the given input password. Have a look at the example given below : 

```javascript
User.findOne({Email : req.body.email}, function(err, user){
    const password = req.body.password; 
    if(err){
        console.log(err)
    }else{
        bcrypt.compare(password, User.password, function(err, compared){
            if(err){
                console.log(err)
            }else{
                console.log(compared)
            }
        })
    }
})
```

## Examples 

- For a complete, working example, refer to this [tutorial](https://www.youtube.com/watch?v=Ud5xKCYQTjM) by Web Dev Simplified that uses ***bcrypt*** for user authentication. 