var express = require('express');
var router = express.Router();
var exec = require('child_process').exec;
var fs = require('fs')

var SHAREDIR = '/home/xc/shareDir/';
var FILENAME = 'example';

/* GET home page. */
router.get('/create', function(req, res, next) {
  executeShell('ls ' + SHAREDIR, function (result) {
    num = result == '' ? 0 : result.split('\n').length - 1;
    dirPath = SHAREDIR + FILENAME + num;
    executeShell('mkdir ' + dirPath, function (mkdirReulst) {
      if ( mkdirReulst != 'error' ) {
        var config = dirPath + '/config.txt';
        writeConfig(config, 'on', 'no');
        res.end('success');
      } else {
        res.end(mkdirReulst);
      }
    });
  });
});

router.get('/show', function(req, res, next) {
  executeShell('ls ' + SHAREDIR, function (result) {
    if (result == '') {
      res.end('');
    } else {
      var files = result.split('\n');

      var list = [];
      for (var i = 0; i <= files.length - 2; i++) {
        var name = files[i];
        var config = SHAREDIR + name + '/config.txt';
        var filePara = readConfig(config).split('\n');
        list.push({
          name: name,
          stat: filePara[0],
          ip: filePara[1]
        })
      }
      res.end(JSON.stringify(list));
    }
  });
});

router.post('/update', function(req, res, next) {
  name = req.query.name;
  stat = req.query.stat;
  ip = req.query.ip;
  config = SHAREDIR + name + '/config.txt';
  if (matchIp(name, getRemoteUrl(req)) && dirStat(name) == true) {
    res.end("can't update");
  } else {
    writeConfig(config, stat, ip);
    res.end('success');
  }
});

router.post('/delete', function(req, res, next) {
  name = req.query.name;
  shareDir = SHAREDIR + name;
  if (name != '' && dirStat(name) == true) {
    executeShell('rm -rf ' + shareDir, function () {
      res.end('success');
    });
  } else {
    res.end("can't delete");
  }
});

function getRemoteUrl (req) {
  return req.connection.remoteAddress;
}

function executeShell (cmdStr, callback) {
  exec(cmdStr, function(err,stdout,stderr){
      if(err) {
          console.log('get weather api error:'+stderr);
          callback('error')
      } else {
          callback(stdout);
      }
  });
}

function readConfig (file) {
  return fs.readFileSync(file, 'utf8'); 
}

function writeConfig (file, stat, ip) {
  fs.writeFileSync(file, stat + '\n' + ip);
}

function matchIp (name, remoteip) {
  ip = new Regexp(readConfig(name).split('\n')[1]);
  return ip.test(remoteip);
}

function dirStat (name) {
  return readConfig(name).split('\n')[0] == 'off' ? false : true
}


module.exports = router;
