## Simple To-Do application on MERN Web Stack



ubuntu@ip-172-31-24-174:~$ cd todo/
ubuntu@ip-172-31-24-174:~/todo$ npm install mongoose
npm WARN todo@1.0.0 No description
npm WARN todo@1.0.0 No repository field.

+ mongoose@5.12.7
added 29 packages from 92 contributors and audited 80 packages in 6.501s

2 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

ubuntu@ip-172-31-24-174:~/todo$ npm fund
todo@1.0.0
├─┬ https://opencollective.com/mongoose
│ └── mongoose@5.12.7
├─┬ https://github.com/sponsors/feross
│ └── safe-buffer@5.2.1
├─┬ https://www.patreon.com/feross
│ └── safe-buffer@5.2.1
└─┬ https://feross.org/support
  └── safe-buffer@5.2.1

ubuntu@ip-172-31-24-174:~/todo$ mkdir models && cd models && touch todo.js
ubuntu@ip-172-31-24-174:~/todo/models$
ubuntu@ip-172-31-24-174:~/todo/models$
ubuntu@ip-172-31-24-174:~/todo/models$
ubuntu@ip-172-31-24-174:~/todo/models$ ls
todo.js
ubuntu@ip-172-31-24-174:~/todo/models$
ubuntu@ip-172-31-24-174:~/todo/models$ vim todo.js
ubuntu@ip-172-31-24-174:~/todo/models$
ubuntu@ip-172-31-24-174:~/todo/models$
ubuntu@ip-172-31-24-174:~/todo/models$ cd ../..
ubuntu@ip-172-31-24-174:~$ ls
todo
ubuntu@ip-172-31-24-174:~$ cd todo/
ubuntu@ip-172-31-24-174:~/todo$ ls
index.js  models  node_modules  package-lock.json  package.json  routes
ubuntu@ip-172-31-24-174:~/todo$ cd routes
ubuntu@ip-172-31-24-174:~/todo/routes$ ls
api.js
ubuntu@ip-172-31-24-174:~/todo/routes$
ubuntu@ip-172-31-24-174:~/todo/routes$
ubuntu@ip-172-31-24-174:~/todo/routes$
ubuntu@ip-172-31-24-174:~/todo/routes$
ubuntu@ip-172-31-24-174:~/todo/routes$ vim api.js
ubuntu@ip-172-31-24-174:~/todo/routes$
ubuntu@ip-172-31-24-174:~/todo/routes$
ubuntu@ip-172-31-24-174:~/todo/routes$
ubuntu@ip-172-31-24-174:~/todo/routes$ cd ..
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$ touch .env
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$ vi .env
ubuntu@ip-172-31-24-174:~/todo$ ls
index.js  models  node_modules  package-lock.json  package.json  routes
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$ vim index.js
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$ curl icanhazip
curl: (6) Could not resolve host: icanhazip
ubuntu@ip-172-31-24-174:~/todo$ sudo curl icanhazip.com
54.225.17.124
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$
ubuntu@ip-172-31-24-174:~/todo$ Connection to ec2-54-225-17-124.compute-1.amazon                                          aws.com closed by remote host.
Connection to ec2-54-225-17-124.compute-1.amazonaws.com closed.
/bin/ssh.exe: line 121: grep: command not found
                                                                               ✘

  /drives/c/Users/oeume/Downloads 
  10/05/2021   12:42.18  ssh -i "projectreload.pem" ubuntu@ec2-3-92-139-85.compute-1.amazonaws.com
Warning: Permanently added 'ec2-3-92-139-85.compute-1.amazonaws.com' (RSA) to the list of known hosts.
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-1047-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 System information disabled due to load higher than 1.0





Last login: Mon May 10 13:41:56 2021 from 73.152.119.181
ubuntu@ip-172-31-24-174:~$ ls
todo
ubuntu@ip-172-31-24-174:~$ cd todo
ubuntu@ip-172-31-24-174:~/todo$ ls
index.js  models  node_modules  package-lock.json  package.json  routes
ubuntu@ip-172-31-24-174:~/todo$ cd routes/
ubuntu@ip-172-31-24-174:~/todo/routes$ ls
api.js
ubuntu@ip-172-31-24-174:~/todo/routes$ vim api.js
ubuntu@ip-172-31-24-174:~/todo/routes$ curl icanhazip.com
3.92.139.85
ubuntu@ip-172-31-24-174:~/todo/routes$
ubuntu@ip-172-31-24-174:~/todo/routes$ vim api.js
ubuntu@ip-172-31-24-174:~/todo/routes$ cat api.js
const express = require ('express');
const router = express.Router();
const todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
ubuntu@ip-172-31-24-174:~/todo/routes$ cd ..
ubuntu@ip-172-31-24-174:~/todo$ vim  index.js
ubuntu@ip-172-31-24-174:~/todo$ cd ..
ubuntu@ip-172-31-24-174:~$ ls
todo
## ubuntu@ip-172-31-24-174:~$ mv todo Todo
ubuntu@ip-172-31-24-174:~$ ls
Todo
ubuntu@ip-172-31-24-174:~$ cd Todo/
ubuntu@ip-172-31-24-174:~/Todo$ ls
index.js  models  node_modules  package-lock.json  package.json  routes
ubuntu@ip-172-31-24-174:~/Todo$ vim index.js
ubuntu@ip-172-31-24-174:~/Todo$ vim package.json
ubuntu@ip-172-31-24-174:~/Todo$ cd routes/
ubuntu@ip-172-31-24-174:~/Todo/routes$ ls
api.js
ubuntu@ip-172-31-24-174:~/Todo/routes$ vim api.js
ubuntu@ip-172-31-24-174:~/Todo/routes$ vim index.js
ubuntu@ip-172-31-24-174:~/Todo/routes$ ls
api.js
ubuntu@ip-172-31-24-174:~/Todo/routes$ cd ..
ubuntu@ip-172-31-24-174:~/Todo$ ls
index.js  models  node_modules  package-lock.json  package.json  routes
ubuntu@ip-172-31-24-174:~/Todo$ vim index.js
ubuntu@ip-172-31-24-174:~/Todo$ ls
index.js  models  node_modules  package-lock.json  package.json  routes
ubuntu@ip-172-31-24-174:~/Todo$ cd routes/
ubuntu@ip-172-31-24-174:~/Todo/routes$ ls
api.js
ubuntu@ip-172-31-24-174:~/Todo/routes$ vim api.js
ubuntu@ip-172-31-24-174:~/Todo/routes$ cat api.js
const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
ubuntu@ip-172-31-24-174:~/Todo/routes$
