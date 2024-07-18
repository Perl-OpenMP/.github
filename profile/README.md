# Perl+OpenMP

_PA&A_ Discord: [Visit #system-programming](https://discord.gg/WKz6bGnn9B)

## Learning OpenMP

* [The OpenMP Common Core: A hands on exploration ǀ Tim Mattson, Intel
](https://www.youtube.com/watch?v=T0csnAirv-U)

## On CPAN

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
