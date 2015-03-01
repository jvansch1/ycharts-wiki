# jQuery UI Components Inventory

List of the various component breakdowns for static-media generators


## Screener UI Components

### UI-Core
* core
* widget
* mouse
* position

### UI Widgets
* slider


## Multichart / Dashboard UI Components

### UI-Core
* core
* widget
* mouse
* position

### UI Interactions
* sortable


## Chart (Companies/indicators/indices) UI Components

custom date filters with calendar popups for chart controls

### UI Core
* core

### UI Widgets
* date-picker -> [custom-css](http://jqueryui.com/themeroller/#!zThemeParams=5d00000100bc05000000000000003d8888d844329a8dfe02723de3e5702a3e7eaab8c886d02bef691bd23673eb0c674bcdf0dddefdfaf3d19875a9abcbf9140dee1a8422d1b9ba93a2358e77130e7704e8bf63016fb8a8ea9c5af820dc90d7f411979f67b0475f1f41e1b23ba809798e2de986201f6ede212bb5a0d43b1b83892dfa213254dc64dd28ca04646877fed408461a67a654d621052b1c718b410aec8a91228475cb5d6b4598d7563cabb73da20f86f43b2da8fd492bf0d6e3f265d19d127fe8e45cf0d1c12edab65ae4f7633993cda62022816cd4cdb1d22ecbb18fbe6aa6e22bde54485e7b883a575f467f496efd482882a54bce367214425983e3a20a329889a8ba9e8f70d93da5d044adc0ecfcdcb5f19876d0c9230cb126db083dbfd8e8fd137c02f717c417342e078212b3b53403203d4eb5b31158bc766dffab84ddb169560e8503c4f370564d5f67abcf64ec61ce367160042d0123c515c1fdf32ee5efeb04c8ace9d915498de2c7b5b942a3c516c7f569044a1e6e905e1f7ff456a5cb275ff0d64f1e8d2b54f8fd7bfce69162d20b95b7f1a3fb6fa51b)
--> when using the custom-css, you will need to update the jquery-image links to match our static-collection system specifically you will need to convert any image path from 
    
    // produced by jquer
    background:url(images/<filename>.png)
    // new version for pipeline/collectstatic, expects files to be in main/css/images, but we use ../ to redirect into main/images/plugin_name 
    background:url(../../main/images/datepicker/<filename>.png)

    // if you use vim you can run this command
    :%s/images\/ui/..\/..\/main/images\/datepicker\/ui/gc  

