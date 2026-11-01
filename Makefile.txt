
CC = gcc
CFLAGS = -std=c11 -Wall -Wextra -Wpedantic -g

COMMON = matrice.c matrice_alloc.c
ALL_SRC = $(COMMON) matrice_io.c matrice_calcul.c

.PHONY: all clean test

all: test_ex1 test_ex2 test_ex3 demo_matrices

test_ex1: test_ex1.c $(COMMON)
	$(CC) $(CFLAGS) -DMAT_TEST_ALLOC $^ -o $@

test_ex2: test_ex2.c $(COMMON) matrice_io.c
	$(CC) $(CFLAGS) -DMAT_TEST_ALLOC $^ -o $@

test_ex3: test_ex3.c $(COMMON) matrice_calcul.c
	$(CC) $(CFLAGS) -DMAT_TEST_ALLOC $^ -o $@

demo_matrices: main.c $(ALL_SRC)
	$(CC) $(CFLAGS) $^ -o $@

test: all
	./test_ex1
	./test_ex2
	./test_ex3

clean:
	rm -f test_ex1 test_ex2 test_ex3 demo_matrices *.o
