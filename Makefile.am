SUBDIRS = JASPER 
ACLOCAL_AMFLAGS = -I m4

EXTRA_DIST = LICENSE.txt

install-special:
	@echo $(SUBDIRS) | tr ' ' '\n' | while read subdir; do \
	  echo "Making install in $$subdir pwd `pwd -P`"; \
	  $(MAKE) $(AM_MAKEFLAGS) -C $$subdir install; \
	done


# Print the value of a variable
print-%:
	@echo -n $($*)
