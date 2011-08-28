---
is_hidden: true
---
Welcome to Jutopia - the personal homepage of Jon Tirs&eacute;n.

<div id="tweets"></div>
<script src="http://widgets.twimg.com/j/2/widget.js"></script>
<script>
new TWTR.Widget({
  version: 2,
  id: 'tweets',
  type: 'profile',
  rpp: 6,
  interval: 6000,
  width: 'auto',
  height: 200,
  theme: {
    shell: {
      background: '#ffffff',
      color: '#000000'
    },
    tweets: {
      background: '#ffffff',
      color: '#000000',
      links: '#eb0707'
    }
  },
  features: {
    scrollbar: true,
    loop: false,
    live: true,
    hashtags: true,
    timestamp: true,
    avatars: false,
    behavior: 'all'
  }
}).render().setUser('tirsen').start();
</script>
