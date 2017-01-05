# Testing and Continuous Integration

官网英文原文地址：http://dev.px4.io/testing-and-ci.html

PX4 offers extensive unit testing and continuous integration facilities. This page provides an overview.

## Testing on the local machine

The following command is sufficient to start a minimal new shell with the PX4 posix port running.

```
make posix_sitl_shell none
```

The shell can then be used to e.g. execute unit tests:

```
pxh> tests mixer
```

Alternatively it is also possible to run the complete unit-tests right from bash:

```
make tests
```

## Testing in the Cloud / CI



