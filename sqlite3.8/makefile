LIBRARY = libsqlite3.so.1
LIBNICK = libsqlite3.so
SHORT   = sqlite3

# the item below cannot be called SHELL because it's a reserved name
SSHELL  = sqlite3

LIBFLAGS = -DSQLITE_USE_URI=1 -DSQLITE_THREADSAFE=1 -DHAVE_USLEEP -DSQLITE_ENABLE_COLUMN_METADATA

all:   $(LIBRARY) $(SSHELL)

debug: $(LIBRARY) $(SSHELL)

debug: export LIBFLAGS := -g -DSQLITE_DEBUG=1 -DDEBUGPRINT $(LIBFLAGS)

$(LIBRARY): $(SHORT).o
	gcc -shared -Wl,-soname,$(LIBRARY) $^ -o $@ -lbinn -lnanomsg
	strip $(LIBRARY)
	ln -sf $(LIBRARY) $(LIBNICK)

$(SHORT).o: $(SHORT).c $(SHORT).h
	gcc -fPIC $(LIBFLAGS) -c $<

$(SSHELL): shell.o $(LIBRARY)
	gcc $< -o $@ -L. -lsqlite3 -ldl -lbinn -lreadline
	strip $(SSHELL)

shell.o: shell/shell.c
	gcc -c -DHAVE_READLINE $< -o $@

install:
	cp $(LIBRARY) /usr/lib
	cd /usr/lib && ln -sf $(LIBRARY) $(LIBNICK)
	cp $(SHORT).h /usr/include
	cp $(SSHELL) /usr/bin

clean:
	rm -f *.o $(LIBRARY) $(LIBNICK) $(SSHELL) tests

tests: test.o seatest.o sqlite3dbg.o
	gcc -g $^ -o $@ -lbinn -lnanomsg -ldl
	cp tests test/tests
	cd test && ./tests

test.o: test/test.c
	gcc -c $< -o $@

seatest.o: ../common/seatest.c
	gcc -c $< -o $@

sqlite3dbg.o: sqlite3.c
	gcc -g -DSQLITE_DEBUG=1 -DDEBUGPRINT -DSQLITE_USE_URI=1 -DSQLITE_THREADSAFE=1 -DHAVE_USLEEP -DSQLITE_ENABLE_COLUMN_METADATA -c $< -o $@

# variables:
#   $@  output
#   $^  all the requirements
#   $<  first requirement
