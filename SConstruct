# from http://www.qandr.org/quentin/writings/debscons
import os, shutil, sys
env = Environment()

# Here's the core info for the package

DEBNAME = "jaredhoberock"
DEBVERSION = "0.01"
DEBMAINT = "Jared Hoberock [jaredhoberock@gmail.com]"
DEBARCH = "noarch"
DEBDEPENDS = "git, ssh, vim-gnome, scons, libboost-dev, libtbb-dev" # what are we dependent on?
DEBDESC = "Jared's development environment"

DEBFILES = [

    # Now we specify the files to be included in the .deb
    # Where they should go, and where they should be copied from.
    # If you have a lot of files, you may wish to generate this 
    # list in some other way.

]
    
# This is the debian package we're going to create
debpkg = '#%s_%s-%s.deb' % (DEBNAME, DEBVERSION, DEBARCH)

# and we want it to be built when we build 'debian'
env.Alias("debian", debpkg)

DEBCONTROLFILE = os.path.join(DEBNAME, "DEBIAN/control")

# This copies the necessary files into place into place.
# Fortunately, SCons creates the necessary directories for us.
for f in DEBFILES:
    # We put things in a directory named after the package
    dest = os.path.join(DEBNAME, f[0])
    # The .deb package will depend on this file
    env.Depends(debpkg, dest)
    # Copy from the the source tree.
    env.Command(dest, f[1], Copy('$TARGET','$SOURCE'))
    # The control file also depends on each source because we'd like
    # to know the total installed size of the package
    env.Depends(DEBCONTROLFILE, dest)

# Now to create the control file:

CONTROL_TEMPLATE = """
Package: %s
Priority: extra
Section: misc
Installed-Size: %s
Maintainer: %s
Architecture: %s
Version: %s
Depends: %s
Description: %s

"""
env.Depends(debpkg,DEBCONTROLFILE )

# The control file should be updated when the SVN version changes
#env.Depends(DEBCONTROLFILE, env.Value(svn_version))

# This function creates the control file from the template and info
# specified above, and works out the final size of the package.
def make_control(target=None, source=None, env=None):
    installed_size = 0
    for i in DEBFILES:
        installed_size += os.stat(str(env.File(i[1])))[6]
    control_info = CONTROL_TEMPLATE % (
        DEBNAME, installed_size, DEBMAINT, DEBARCH, DEBVERSION,
        DEBDEPENDS, DEBDESC)
    f = open(str(target[0]), 'w')
    f.write(control_info)
    f.close()
    
# We can generate the control file by calling make_control
env.Command(DEBCONTROLFILE, None, make_control)

# And we can generate the .deb file by calling dpkg-deb
env.Command(debpkg, DEBCONTROLFILE,
            "fakeroot dpkg-deb -b %s %s" % ("%s" % DEBNAME, "$TARGET"))

