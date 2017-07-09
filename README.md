Dreambox BSP layer for dm800se only

Using https://github.com/OpenPLi/openpli-oe-core as base.

Open a terminal inside "openpli-oe-core" folder and enter:
```
git submodule add https://github.com/ims21/dm800se-bsp.git
```
Now edit the "Makefile" file and add the BSP layer to it:
```
	$(CURDIR)/meta-openpli \
	$(CURDIR)/meta-dream \
+	$(CURDIR)/dm800se \
	$(CURDIR)/meta-vuplus \
```
Remove the "/dm800se/classes/image_types_nfi.bbclass" file because it doesn't support "jffs2nfi" images.

We're independent so if you think you can help you're welcome to send us merge requests :).
