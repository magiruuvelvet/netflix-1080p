# netflix-1080p
~~Chrome extension to play Netflix in 1080p~~

Mirror for [magiruuvelvet/LightweightQtDRMStreamViewer](https://github.com/magiruuvelvet/LightweightQtDRMStreamViewer)

- `cadmium-playercore-5.0008.544.011-1080p.js`: 1080p patched
- `cadmium-playercore-5.0008.572.011-1080p.js`: 1080p patched
- `cadmium-playercore-5.0008.864.051.js`: ??? (untested)

# How it works

The core of Netflix playback lies in JavaScript: specifically its "cadmium playercore" JS. The way Netflix (poorly) enforces 1080p to only be played back on ChromeOS devices is through these lines of JavaScript:

```javascript
a = /CrOS/.test(a.userAgent);
this.Fma = this.Aw = q.Gu.PV;
this.Qm = [x.$l.nV];
this.oo = [x.V.vA, x.V.wA];
a && this.oo.push(x.V.TH);
```

What it is doing is testing your User-agent for the "CrOS" string anywhere in it. If the search returns true, it appends the 1080p profile to the profile playback array (what this line `a && this.oo.push(x.V.TH);` is doing). If it returns false, it does nothing. The playback profile array is set up like so: `this.oo = [x.V.vA, x.V.wA];`, x.V.vA is the SD profile and x.V.wA is the 720p profile.

After reading this you think the easy solution would be to just change the User-agent to make it contain the string "CrOS" right? Not that simple. ChromeOS apparently has a different DRM implementation than chrome, even though both use Widevine. I could never get it to work when I tried, Netflix always threw license errors. The next easiest thing to do is just delete the conditional to append 1080p and just make the 1080p profile apart of the regular profiles (`this.oo = [x.V.vA, x.V.wA];` -> `this.oo = [x.V.vA, x.V.wA, x.V.TH];`). This works perfectly.

# How to use

Add this lines to your Netflix provider files once you read the documentation.

```
urlInterceptor:true
urlInterceptorPattern:(.*\:\/\/assets\.nflxext\.com\/.*\/ffe\/player\/html\/.*)|(.*\:\/\/www\.assets\.nflxext\.com\/.*\/ffe\/player\/html\/.*)
urlInterceptorTarget:https://rawgit.com/magiruuvelvet/netflix-1080p/master/[file].js
```

Replace `[file]` with one of the files listed above. The ones with the 1080p status are confirmed to be working.

# Why?

Why not.

# Notes

This may raise your CPU usage since Netflix was never intended to be played back in 1080p on Chrome.

Make sure to clear your browser cache, as the cached playercore will override the modified one and you will not be able to play 1080p.
