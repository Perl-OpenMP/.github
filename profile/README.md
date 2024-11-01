# Perl+OpenMP

* discussions, _visit_: [#system-programming](https://discord.gg/WKz6bGnn9B) on the PA&A Discord
* old school email discussions - **[sign up](https://sourceforge.net/projects/perl-openmp/lists/perl-openmp-discuss)** @ _sourceforge.net_ [[archive](https://sourceforge.net/p/perl-openmp/mailman/perl-openmp-discuss/)]
<br/>(*NOTE: I am working on email deliverability issues for _gmail_ users, maybe other "free" email sites!)

## Learning OpenMP (Videos)

* [The OpenMP Common Core: A hands on exploration
](https://www.youtube.com/watch?v=T0csnAirv-U) (3.5 hrs - Tim Mattson, Intel, 2019)
* [Using OpenMP in Perl](https://www.youtube.com/watch?v=wHjmxGJd7rQ) (2hrs - Houston PM, May 2021)
* [Introduction to OpenMP for Perl Programmers](https://www.youtube.com/watch?v=4SRR2-uGg40) (50m - TPRC 2022)
* [State of the Art: Perl and Multithreading via OpenMP](https://www.youtube.com/watch?v=4SRR2-uGg40) (50m - TPRC 2022)
* [Intermediate OpenMP for Perl Programmers](https://www.youtube.com/watch?v=_pzG5DerDT0) (50m - TPRC 2024)

## OpenMP Modules On CPAN

* [OpenMP](https://metacpan.org/pod/OpenMP) - metapackage (includes following 2 modules w/_lagniappe_)
* [OpenMP::Simple](https://metacpan.org/pod/OpenMP::Simple) - helper C macros and runtime functions
* [OpenMP::Environment](https://metacpan.org/pod/OpenMP::Environment) - for managing variables in `%ENV` important to the OpenMP runtime

## Quick Start
### Install Modules

`cpanm OpenMP # installs all of the above modules`

### First Program

Create this Perl file and execute on *nix using a `perl` built using `gcc`:

```perl
#!/usr/bin/env perl
use strict;
use warnings;
   
use OpenMP;
   
use Inline (
    C    => 'DATA',
    with => qw/OpenMP::Simple/,
);
   
my $omp = OpenMP->new;
   
for my $want_num_threads ( 1 .. 8 ) {
    $omp->env->omp_num_threads($want_num_threads);
 
    $omp->env->assert_omp_environment; # (optional) validates %ENV
 
    # call parallelized C function
    my $got_num_threads = _check_num_threads();
 
    printf "%0d threads spawned in ".
            "the OpenMP runtime, expecting %0d\n",
              $got_num_threads, $want_num_threads;
}
 
__DATA__
__C__
 
/* C function parallelized with OpenMP */
int _check_num_threads() {
  int ret = 0;
    
  PerlOMP_GETENV_BASIC
   
  #pragma omp parallel
  {
    #pragma omp single
    ret = omp_get_num_threads();
  }
 
  return ret;
}
__END__
```
**Expected Output:**

```
1 threads spawned in the OpenMP runtime, expecting 1
2 threads spawned in the OpenMP runtime, expecting 2
3 threads spawned in the OpenMP runtime, expecting 3
4 threads spawned in the OpenMP runtime, expecting 4
5 threads spawned in the OpenMP runtime, expecting 5
6 threads spawned in the OpenMP runtime, expecting 6
7 threads spawned in the OpenMP runtime, expecting 7
8 threads spawned in the OpenMP runtime, expecting 8
```

### Next Program

That's up to you. Please share!

## Resources
### Most Used OpenMP Things (the _Common Core_):

* `#pragma omp parallel`
* `int omp_get_thread_num()`
* `int omp_get_num_threads()`
* `export OMP_NUM_THREADS=N`
* `#pragma omp barrier`
* `#pragma omp critical`
* `#pragma omp for`
* `#pragma omp parallel for`
* `reduction(op:list)`
* `schedule(dynamic[,chunk])`
* `shcedule(static,[,chunk])`
* `private(list)`, `firstprivate(list)`, `shared(list)`
* `nowait`
* `#pragma omp single`
* `#pragma omp task`
* `#pragma omp taskwait`


### Tutorials

* PDF [The OpenMP Common Core: A journey back to the roots of OpenMP](https://www.openmp.org/wp-content/uploads/SC19-Mattson-Common-Core.pdf) (Mattson, 2019) [code](https://github.com/Perl-OpenMP/openmp-tutorial)
* PDF [A Guide To OpenMP](https://cscads.rice.edu/openmp-combined.pdf) (Estrade, 2011)

### Books

* Amazon: [The OpenMP Common Core: Making OpenMP Simple Again (Scientific and Engineering Computation)](https://www.amazon.com/OpenMP-Common-Core-Engineering-Computation/dp/0262538865) (ISBN: 0262538865)

### Videos

* [How Popular is OpenMP?](https://www.youtube.com/watch?v=VkOou-_hhTU) (2023)

