---
layout: default
title: FirstVideo
---        
        <meta http-equiv="content-type" content="text/html; charset=UTF-8" />
        <title>Mediasite Player</title>
        <script src="Javascript/Scripts/jquery.js" type="text/javascript"></script>
        <script src="Javascript/Scripts/Mediasite.Core.js" type="text/javascript"></script>
        <script src="Javascript/Scripts/standalone-manifest.js" type="text/javascript"></script>
        <script type="text/javascript">
var port25Supported = false;
var playerLoaders = [];
playerLoaders[0]={ Plugin:2, LoaderLocation:"SilverlightLoader.html" };
playerLoaders[1]={ Plugin:36, LoaderLocation:"NewStandardPlayer.html?plugin=HTML5" };
playerLoaders[2]={ Plugin:1, LoaderLocation:"NewStandardPlayer.html?plugin=WindowsMedia" };
playerLoaders[3]={ Plugin:1, LoaderLocation:"ScreenReader.html?plugin=WindowsMedia" };
playerLoaders[4]={ Plugin:2, LoaderLocation:"ScreenReader.html?plugin=Silverlight" };
playerLoaders[5]={ Plugin:36, LoaderLocation:"ScreenReader.html?plugin=HTML5" };
</script>


        <script type="text/javascript" >
            var i;

            function playerDetect() {

                var mediaPlugins = { None: 0, WindowsMedia: 1, Silverlight: 2, Html5: 4, Flash: 8 };
                var plugins = mediaPlugins.None;

                function detectSL() {
                    try {
                        if (window.ActiveXObject) {
                            try {
                                var control = new ActiveXObject("AgControl.AgControl");
                                plugins = plugins | mediaPlugins.Silverlight;
                                control = null;
                                return;
                            }
                            catch (e) {
                            }
                        }
                        var p = navigator.plugins["Silverlight Plug-In"];
                        if (p) {
                            plugins = plugins | mediaPlugins.Silverlight;
                        }
                    }
                    catch (e2) {
                    }
                }

                function detectWMP() {
                    var i;
                    try {
                        if (window.ActiveXObject) {
                            try {
                                var control = new ActiveXObject("WMPlayer.OCX.7");
                                plugins = plugins | mediaPlugins.WindowsMedia;
                                control = null;
                                return;
                            }
                            catch (e) {
                            }
                        }
                        if (port25Supported && navigator.userAgent.toLowerCase().indexOf('firefox') > -1) {
                            for (i = 0; i < navigator.plugins.length; i++) {
                                if (navigator.plugins[i].filename === "np-mswmp.dll") {
                                    plugins = plugins | mediaPlugins.WindowsMedia;
                                    return;
                                }
                            }
                        }
                    }
                    catch (e2) {
                    }
                }

                function detectHTML5() {
                    try {
                        var video = document.createElement("video");
                        var playable = video.canPlayType('video/mp4; codecs="avc1.42E01E, mp4a.40.2"');
                        if (playable === 'probably' || playable === 'maybe') {
                            plugins = plugins | mediaPlugins.Html5;
                        }
                    }
                    catch (e) {
                    }
                }

                function detectFlash() {
                    try {
                        if (window.ActiveXObject) {
                            try {
                                var control = new ActiveXObject('ShockwaveFlash.ShockwaveFlash.7');
                                plugins = plugins | mediaPlugins.Flash;
                                control = null;
                                return;
                            }
                            catch (e) {
                            }
                        }
                        var plugin = navigator.plugins['Shockwave Flash'];
                        if (plugin) {
                            plugins = plugins | mediaPlugins.Flash;
                        }
                    }
                    catch (e2) {
                    }
                }

                function filterByMediaTypes() {
                    var i, compatiblePlugins = mediaPlugins.None;
                    if (typeof Mediasite.PlaybackManifest !== "undefined" && typeof Mediasite.PlaybackManifest.VideoUrls !== "undefined") {
                        for (i = 0; i< Mediasite.PlaybackManifest.VideoUrls.length; i++) {
                            switch (Mediasite.PlaybackManifest.VideoUrls[i].MimeType) {
                            case "video/mp4":
                                compatiblePlugins = compatiblePlugins | mediaPlugins.Silverlight | mediaPlugins.Html5 | mediaPlugins.Flash;
                                break;
                            case "video/x-ms-wmv":
                                compatiblePlugins = compatiblePlugins | mediaPlugins.Silverlight | mediaPlugins.WindowsMedia;
                                break;
                            }
                        }
                    }
                    plugins = plugins & compatiblePlugins;
                }

                detectSL();
                detectWMP();
                detectHTML5();
                detectFlash();
                filterByMediaTypes();
                var loaderFound = false;


                if (typeof playerLoaders === "undefined" || !playerLoaders) {
                    var selectedPlugin = "Unknown";
                    if ((plugins & mediaPlugins.WindowsMedia) === mediaPlugins.WindowsMedia) {
                        selectedPlugin = "WindowsMedia";
                    }
                    else if ((plugins & mediaPlugins.Silverlight) === mediaPlugins.Silverlight) {
                        selectedPlugin = "Silverlight";
                    }
                    else if ((plugins & mediaPlugins.Html5) === mediaPlugins.Html5) {
                        selectedPlugin = "HTML5";
                    }
                    else if ((plugins & mediaPlugins.Flash) === mediaPlugins.Flash) {
                        selectedPlugin = "Flash";
                    }
                    window.location.href = 'StandardPlayer.html?plugin=' + selectedPlugin;
                }
                else {
                    for (i = 0; i < playerLoaders.length; i++ ) {
                        if ((plugins & playerLoaders[i].Plugin) > 0) {
                            window.location.href = playerLoaders[i].LoaderLocation;
                            loaderFound = true;
                            break;
                        }
                    }
                }

                if (!loaderFound) {
                    var ieVersion = Mediasite.BrowserDetect.InternetExplorer();
                    var errorMessage = 'The Mediasite presentation cannot be played.';

                    if (ieVersion && ieVersion > 9) {
                        errorMessage += "\nIf running Windows 8, you may need to switch to the desktop view.";
                    }

                    alert(errorMessage);
                }
                
            }
        </script>
    </head>
    <body onload="playerDetect();">        
        <noscript>
            <p>Javascript is required to view this content</p>
	    </noscript>
    </body>