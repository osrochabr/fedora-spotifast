# Copr "make_srpm" hook, run as root in a network-enabled chroot:
#   make -f .copr/Makefile srpm outdir=<dir> spec=<path/to/spotifast.spec>
specdir := $(dir $(spec))
work := $(shell mktemp -d)

srpm:
	dnf -y install git rpm-build rpmautospec rpmdevtools
	# the clone belongs to another user; let git and rpmautospec read it
	git config --system --add safe.directory '*'
	rpmautospec process-distgit $(spec) $(work)/spotifast.spec
	cp $(specdir)spotifast-update-public-key.pem $(work)/
	spectool --get-files --directory $(work) $(work)/spotifast.spec
	rpmbuild -bs --define '_sourcedir $(work)' --define '_srcrpmdir $(outdir)' $(work)/spotifast.spec
