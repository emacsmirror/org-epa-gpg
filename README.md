# org-epa-gpg
[![MELPA][melpa-badge]][melpa-package]
[![MELPA Stable][melpa-stable-badge]][melpa-stable-package]
[![Buy me a coffee][bmc-badge]][bmc-link]
[![Liberapay][lp-badge]][lp-link]
[![PayPal][ppl-badge]][ppl-link]

This is a patch that attempts to fix image inlining (C-c C-x C-v) in
the org-mode for images that are encrypted AND end with ".gpg" extension.

## Install and run

For installation simply issue this command, it'll download the lisp module
from this repo directly under the latest stable tag (or replace the tag
version with `master` for the latest code).

```emacs-lisp
(let ((file (make-temp-file "" nil ".el")))
(url-copy-file
 "https://raw.githubusercontent.com/KeyWeeUsr/org-epa-gpg/3.0.0/org-epa-gpg.el"
 file t)
(package-install-file file))
```

To start, add these mandatory lines to your config file:

```emacs-lisp
(use-package org-epa-gpg
  :ensure t
  :after epa-file
  :config (org-epa-gpg-enable))
```

## Notes on purging

The cleaning of the decrypted content (in /tmp via (make-temp-file) )
is currently done via patching (org-remove-inline-images) with a custom
hook (so that it's purged on (C-c C-x C-v) untoggling and via attaching
to multiple standard hooks.

### Attempted

What I attempted to make it less insane:
* purge the files right after decrypting and loading
  -> this causes an empty rectangle to be shown in the buffer
* hook to standard hooks such as:
  * buffer-list-update-hook
    -> basically kills Emacs by making it frozen due to too many updates
    and the initial startup with few hundreds of buffers opened is
    a time to go for a coffee
  * change-major-mode / after-change-major-mode-hook / org-mode-hook
    -> is unreliable from security perspective due to being too slow

### Current behavior

So I added the purging to these hooks so that an action of "going away"
from the org buffer or from Emacs alone, which would potentially endanger
the unencrypted data, would also purge them. It's also trying to minimize
their overall presence too `org-epa-gpg-purging-hooks`.

A timer could possibly be added with a very short interval, but it doesn't seem
feasible if the amount of images is more than a few (large Org documents) due
to 1:1 (image:purger) relationship in `list-timers' possibly hogging Emacs down
readability-/resource-wise.  Or at least, if turned on by default.

[melpa-badge]: http://melpa.org/packages/org-epa-gpg-badge.svg
[melpa-package]: http://melpa.org/#/org-epa-gpg
[melpa-stable-badge]: http://stable.melpa.org/packages/org-epa-gpg-badge.svg
[melpa-stable-package]: http://stable.melpa.org/#/org-epa-gpg
[bmc-badge]: https://img.shields.io/badge/-buy_me_a%C2%A0coffee-gray?logo=buy-me-a-coffee
[bmc-link]: https://www.buymeacoffee.com/peterbadida
[ppl-badge]: https://img.shields.io/badge/-paypal-grey?logo=paypal
[ppl-link]: https://paypal.me/peterbadida
[lp-badge]: https://img.shields.io/badge/-liberapay-grey?logo=liberapay
[lp-link]: https://liberapay.com/keyweeusr
