# node-client-info
Node: display client information with nodejs behind the nginx reverse-proxy 

1. Install geoip-lite:
```
npm install geoip-lite -s
```

2. Sample code:
```
var app = express();
app.set('trust proxy', true);

var geoip = require('geoip-lite');

app.get('/test', function(req, res){

  var conn_from_user_ip2 = req.headers['x-forwarded-for'] || req.socket.remoteAddress
  var conn_from_user_ip = req.headers['x-real-ip'] || req.connection.remoteAddress
  console.log(`log: conn_from_user_ip=${conn_from_user_ip}`)
  console.log(`log: conn_from_user_ip2=${conn_from_user_ip2}`)

  console.log('Headers: ' + JSON.stringify(req.headers));
  console.log('IP: ' + JSON.stringify(conn_from_user_ip));

  var geo = geoip.lookup(conn_from_user_ip);

  console.log("Browser: " + req.headers["user-agent"]);
  console.log("Language: " + req.headers["accept-language"]);
  console.log("Country: " + (geo ? geo.country : "Unknown"));
  console.log("Region: " + (geo ? geo.region : "Unknown"));

  console.log(geo);
  
})
```

3. Add in /etc/nginx.conf at the bottom of 'http {}' directive :
```
proxy_set_header  X-Real-IP  $remote_addr;
```

