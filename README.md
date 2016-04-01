Big flow

# If archives present -- run datalad's pipeline to extract/update state of "<branch>-processed"
 and return back to original branch
  
# after normal `debian/rules install` (or may be only after dh_auto_install so we still
  have a single tree??) run dh_annex which would

 - initiate an annex repo under "main package" installation path (config mainpackage, mainpath)
   and add original one as an origin remote (we actually need only git-annex branch really).
   Probably initiate straight in direct mode... ?  merge into it git-annex branch so we
   get access
   - we need some configuration to add additional (git) remotes to be added
   whenever installing this git-annex repo
   - since we (at least for FreeSurfer) are doomed to add those repositories to target
   installation (content is not referred by annex via urls), we as well could include
   entire repository into .deb with the repositories already listed
   - so configuration could be XS-GIT-ANNEXES, which would be a list of remotes to add.
   Since names do not really matter (or do they? e.g. for prioritarization) we could
   just add as origin1, origin2, etc
 - ACTUALLY we need some "persistent" branch so we could later upgrade/switch between
   revisions seamlessly.  Let's create a dedicated branch ("debian-annex"?) which,
   upon successful build we should push to original repository tagged accordingly
   ("debian-annex/<DEBIAN-REVISION>")
 - ACTUALLY #2 we DON'T NEED a persistent branch probably since package itself would need
   to contain full repository anyways, so it would be replaced in its entirety
   - Alternative could be that pkg doesn't ship the git repository but we instructs which
   to install (and where, since would be outside of the pkg content), so upon upgrades
   we just need to fetch and which treeish to checkout...
   **pros**: more efficient; **cons**: we would need to publish our clone of freesurfer repo
   online since it would contain "<branch>-processed" and adjusted git-annex with urls to
   tarballs... so may be later, for now let's aim for self contained pkg??
 - probably first collect a list of all keys known to annex, assemble a lookup table
 - for every file (matching expr? excluding some, e.g. binaries according to mime types)
   - determine if there is a matching its checksum key known to annex and if so -
   - just 'annex add' 
   - verify that now whereis for that file contains urls (for paranoids config checkurls to verify
   that urls are reachable, there could be annex command for that already)...
   - drop file's content
   
 - **problem**:  if some files are installed in dh_install stage, then we would fail...
   
   - we could of cause just go through all the packages AFTER full install and manually
     establish symlinks to a hypothetical .git/annex/objects ... or may be there is a
     native annex way as with
   - **resolution** for now just care about sorting out at the single package stage
     (under debian/tmp or debian/only-package)

 - config:  we should allow to provide additional annex repos at the packaging time and then
   to extend later... probably under smth like '/etc/dh-annex/sources/<source-package-name>'
   allowing to give them names as well.  Eventually should allow to set priorities

 - we might have multiple annexes within a single/across packages

# 
