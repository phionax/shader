running on glslCanvas  ()



html (search "(REPLACING THIS LINE, PLACE HOLDER)", replace that and run the html):



<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>GlslCanvas</title>

        <script type="text/javascript" src="build/GlslCanvas.js"></script>
        <script type="text/javascript" src="https://cdn.rawgit.com/github/fetch/master/fetch.js"></script>
		<script type="text/javascript" src="https://rawgit.com/patriciogonzalezvivo/glslCanvas/master/dist/GlslCanvas.js"></script>

        <style>
            body {
                background: #101515;
            }

            #glslCanvas {
                position: absolute;
                top: 50%;
                left: 50%;
                transform: translate(-50%,-50%);
            }​

            .blank {

            }

            #credits {
                position: absolute; 
                bottom: 10px; 
                right: 15px; 
                text-align: right;
                background: rgba(0,0,0,.5);
                padding: 12px;
                padding-top: 5px;
                padding-bottom: 5px;
                margin: 0px
            }

            .label {
                color: white;
                font-family: Helvetica, Arial, sans-serif;
                text-decoration: none; 
                line-height: 0.0;
            }

            #title {
                font-size: 24px;
                font-weight: 600;
            }

            #author {
                font-size: 14px; 
                font-style: italic;
                font-weight: 100;
            }
        </style>

    </head>
    <body>
        <canvas id="glslCanvas" data-fragment="


(REPLACING THIS LINE, PLACE HOLDER)



" width="800" height="600"></canvas>

        <div id="credits">
            <p class="label" id="title"></p>
            <p class="label" id="author"></p>
        </div>
    </body>

    <script>
        var canvas = document.getElementById("glslCanvas");
        var sandbox = new GlslCanvas(canvas);
        var texCounter = 0;
        var sandbox_content = "";
        var sandbox_title = "";
        var sandbox_author = "";
        var sandbox_thumbnail = ""; 
        canvas.style.width = '100%';
        canvas.style.height = '100%';

        function parseQuery (qstr) {
            var query = {};
            var a = qstr.split('&');
            for (var i in a) {
                var b = a[i].split('=');
                query[decodeURIComponent(b[0])] = decodeURIComponent(b[1]);
            }
            return query;
        }

        function load(url) {
            // Make the request and wait for the reply
            fetch(url)
                .then(function (response) {
                    // If we get a positive response...
                    if (response.status !== 200) {
                        console.log('Error getting shader. Status code: ' + response.status);
                        return;
                    }
                    // console.log(response);
                    return response.text();
                })
                .then(function(content) {
                    sandbox_content = content;
                    sandbox.load(content);

                    var title = addTitle();
                    var author = addAuthor();
                    if ( title === "unknown" && author === "unknown") {
                        document.getElementById("credits").style.visibility = "hidden";
                    } else {
                        document.getElementById("credits").style.visibility = "visible";
                    }

                    addMeta({
                                'title' : title + ' by ' + author,
                                'type' : 'website',
                                'url': window.location.href,
                                'image': sandbox_thumbnail
                            })                 
                })
        }

        function addTitle() {
            var result = sandbox_content.match(/\/\/\s*[T|t]itle\s*:\s*([\w|\s|\@|\(|\)|\-|\_]*)/i);
            if (result && !(result[1] === ' ' || result[1] === '')) {
                sandbox_title = result[1].replace(/(\r\n|\n|\r)/gm, '');
                var title_el = document.getElementById("title").innerHTML = sandbox_title;
                return sandbox_title;
            }
            else {
                return "unknown";
            }
        }

        function addAuthor() {
            var result = sandbox_content.match(/\/\/\s*[A|a]uthor\s*[\:]?\s*([\w|\s|\@|\(|\)|\-|\_]*)/i);
            if (result && !(result[1] === ' ' || result[1] === '')) {
                sandbox_author = result[1].replace(/(\r\n|\n|\r)/gm, '');
                document.getElementById("author").innerHTML = sandbox_author;
                return sandbox_author;
            }
            else {
                return "unknown";
            }
        }

        function addMeta(obj) {
            for (var key in obj) {
                var meta = document.createElement('meta');
                meta.setAttribute('og:'+key, obj[key]);
                document.getElementsByTagName('head')[0].appendChild(meta);
            }              
        }

        var query = parseQuery(window.location.search.slice(1));
        if (query && query.log) {
            sandbox_thumbnail = 'https://thebookofshaders.com/log/' + query.log + '.png';
            load('https://thebookofshaders.com/log/' + query.log + '.frag');
        }

        if (window.location.hash !== '') {
            var hashes = location.hash.split('&');
            for (var i in hashes) {
                var ext = hashes[i].substr(hashes[i].lastIndexOf('.') + 1);
                var path = hashes[i];

                // Extract hash if is present
                if (path.search('#') === 0) {
                    path = path.substr(1);
                }

                if (ext === 'frag') {
                    load(path);
                }
                else if (ext === 'png' || ext === 'jpg' || ext === 'PNG' || ext === 'JPG') {
                    sandbox.setUniform("u_tex"+texCounter.toString(), path);
                    texCounter++;
                }
            }
        }
    </script>
    <script>
        (function(i,s,o,g,r,a,m){i["GoogleAnalyticsObject"]=r;i[r]=i[r]||function(){
        (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
        m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
        })(window,document,"script","//www.google-analytics.com/analytics.js","ga");
        ga("create", "UA-18824436-2", "auto");
        ga("send", "pageview");
    </script>
</html>