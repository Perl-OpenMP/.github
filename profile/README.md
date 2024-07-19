# Perl+OpenMP

* discussions, _visit_: [#system-programming](https://discord.gg/WKz6bGnn9B) on the PA&A Discord
* old school email discussions - **[sign up](https://perl-guilds.net/mailman/listinfo/openmp_perl-guilds.net)** @ _perl-guilds.net_ [[archive](https://perl-guilds.net/pipermail/openmp_perl-guilds.net/)]
* (*NOTE: I am working on email deliverability issues for _gmail_ users, maybe other "free" email sites!)

## Learning OpenMP (Videos)

* [The OpenMP Common Core: A hands on exploration
](https://www.youtube.com/watch?v=T0csnAirv-U) (3.5 hrs - Tim Mattson, Intel, 2019)
* [Using OpenMP in Perl](https://www.youtube.com/watch?v=wHjmxGJd7rQ) (2hrs - Houston PM, May 2021)
* [Introduction to OpenMP for Perl Programmers](https://www.youtube.com/watch?v=4SRR2-uGg40) (50m - TPRC 2022)
* [State of the Art: Perl and Multithreading via OpenMP](https://www.youtube.com/watch?v=4SRR2-uGg40) (50m - TPRC 2022)
* [Intermediate OpenMP for Perl Programmers](https://www.youtube.com/watch?v=_pzG5DerDT0) (50m - TPRC 2024)

## OpenMP Modules On CPAN

* [OpenMP::Simple](https://metacpan.org/pod/OpenMP::Simple)
* [OpenMP::Environment](https://metacpan.org/pod/OpenMP::Environment)

## Quick Start
### Install Modules

`cpanm OpenMP::Environment OpenMP::Simple`

### First Program

Create this Perl file and execute on *nix using a `perl` built using `gcc`:

```perl
#!/usr/bin/env perl
use strict;
use warnings;
   
use OpenMP::Simple;
use OpenMP::Environment;
   
use Inline (
    C    => 'DATA',
    with => qw/OpenMP::Simple/,
);
   
my $env = OpenMP::Environment->new;
   
for my $want_num_threads ( 1 .. 8 ) {
    $env->omp_num_threads($want_num_threads);
 
    $env->assert_omp_environment; # (optional) validates %ENV
 
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
 
  PerlOMP_UPDATE_WITH_ENV__NUM_THREADS /* <~ MACRO x OpenMP::Simple */
 
  #pragma omp parallel
  {
    #pragma omp single
    ret = omp_get_num_threads();
  }
 
  return ret;
}

__END__
```

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

## Resources

### Tutorials

* PDF [The OpenMP Common Core: A journey back to the roots of OpenMP](https://www.openmp.org/wp-content/uploads/SC19-Mattson-Common-Core.pdf) (Mattson, 2019) [code](https://github.com/Perl-OpenMP/openmp-tutorial)
* PDF [A Guide To OpenMP](https://cscads.rice.edu/openmp-combined.pdf) (Estrade, 2011)

### Books

* Amazon: [The OpenMP Common Core: Making OpenMP Simple Again (Scientific and Engineering Computation)](https://www.amazon.com/OpenMP-Common-Core-Engineering-Computation/dp/0262538865) (ISBN: 0262538865)

### Videos

* [How Popular is OpenMP?](https://www.youtube.com/watch?v=VkOou-_hhTU) (2023)

