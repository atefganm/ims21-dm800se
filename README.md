Dreambox BSP layer for dm800se only

Using https://github.com/OpenPLi/openpli-oe-core as base.

Open a terminal inside "openpli-oe-core" folder and enter:
```
git submodule add https://github.com/ims21/dm800se.git
```
Now edit the "Makefile" file and add the BSP layer to it:
```
	$(CURDIR)/meta-openpli \
	$(CURDIR)/meta-dream \
+	$(CURDIR)/dm800se \
	$(CURDIR)/meta-vuplus \
```
Remove the "/dm800se/classes/image_types_nfi.bbclass" file because it doesn't support "jffs2nfi" images.

If you want remove this BSP layer, use:
# Remove the submodule entry from .git/config
git submodule deinit -f path/to/submodule

# Remove the submodule directory from the superproject's .git/modules directory
rm -rf .git/modules/path/to/submodule

# Remove the entry in .gitmodules and remove the submodule directory located at path/to/submodule
git rm -f path/to/submodule
