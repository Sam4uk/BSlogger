# BSLogger - extremely simple logger for C++

## Overview

The idea behind BSlogger is to provide an elementary logging class for C++ projects that don't need any advanced logging features.

BSlogger is:

  * header only
  * single class
  * very low overhead

BSlogger supports:

  * colored output
  * time and snapshot logging
  * several log levels, log level control
  * automatic function info

## Example usage

Basically all the features are shown in bslogger_examples.cpp

```
  LOG_INIT_CERR();

  // Log some stuff. The initial loglevel is
  // INFO and lower (INFO, NOTICE, WARNING, ERROR)
  // so the debug info is not displayed
  log(LOG_DEBUG) << "Some text\n";
  log(LOG_INFO) << "Some text\n";
  log(LOG_NOTICE) << "Some text\n";
  log(LOG_WARNING) << "Some text\n";
  log(LOG_ERR) << "Some text\n";
  log(LOG_CRIT) << "Some text\n";
  log(LOG_ALERT) << "Some text\n";
  log(LOG_EMERG) << "Some text\n";

  // Change the log level
  log.set_log_level(LOG_DEBUG);
  /home/sam4uk/Загальнодоступні/VERIFY_URL/include/BSlogger/README.md

  // Now the debug info is displayed
  log(LOG_DEBUG) << "Some text\n";

  // Everything that has a operator<< method
  // for ostreams can be logged
  float x = 3.1415;

  log(LOG_DEBUG) << "The value of x is " << x
                 << ", the address is " << &x
                 << '\n';

  // You can add time snapshots
  log.add_snapshot("before_sleep");
  std::this_thread::sleep_for(std::chrono::milliseconds(2000));
  
  // Log the time since the last snap added
  log.time_since_last_snap();

  // Log the time since a particular snap was added
  log.time_since_snap("before_sleep");

  // Log the time since the log was initialized
  log.time_since_start();
```

Output:

```
[    INFO   ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: Some text
[   NOTICE  ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: Some text
[  WARNING  ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: Some text
[   ERROR   ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: Some text
[  CRITICAL ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: Some text
[   ALERT   ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: Some text
[ EMERGENCY ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: Some text
[   DEBUG   ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: Some text
[   DEBUG   ][ 2021-01-01T13:03:44 ][ int main(int, char **) ]: The value of x is 3.1415, the address is 0x7fff91505e20
|============================== | 99.94% | 15.14 Munits/s | 00-00:01:06 | 00-00:00:01
[   NOTICE  ][ 2021-01-01T13:04:53 ][ int main(int, char **) ]: 2s since snap 'before_sleep'
[   NOTICE  ][ 2021-01-01T13:04:53 ][ int main(int, char **) ]: 2s since snap 'before_sleep'
[   NOTICE  ][ 2021-01-01T13:04:53 ][ int main(int, char **) ]: 69s since instantiation  
```

## Advanced usage

The macros `LOG_INIT_COUT()`, `LOG_INIT_CERR()` and `LOG_INIT_CLOG()` are shorthands for `logger log(std::cout, __PRETTY_FUNCTION__)`, `logger log(std::cerr, __PRETTY_FUNCTION__)` and `logger log(std::clog, __PRETTY_FUNCTION__)`. Any `std::ostream` can be passed to the `logger()` constructor. The two constructors are:

  * `logger(std::ostream&, std::string)` - contructs a BSlogger logging to `ostream` and presenting the supplied string as the namespace.
  * `logger(std::ostream&, unsigned, std::string)` - construcs a BSlogger using a non default log-level, otherwise same as above.

The log levels are:
|Value|  Severity   |   Keyword   |Description                      |Condition                                                                            |
|:---:|:-----------:|:-----------:|---------------------------------|-------------------------------------------------------------------------------------|
|  0  |  Emergency  | `LOG_EMERG` |System is unusable               |A panic condition.                                                                   |
|  1  |    Alert    | `LOG_ALERT` |Action must be taken immediately |A condition that should be corrected immediately, such as a corrupted system database|
|  2  |   Critical  | `LOG_CRIT`  |Critical conditions              |Hard device errors.                                                                  |
|  3  |    Error    |  `LOG_ERR`  |Error conditions                 |                                                                                     |
|  4  |   Warning   | `LOG_WARN`  |Warning conditions               |                                                                                     |
|  5  |   Notice    |`LOG_NOTICE` |Normal but significant conditions|Conditions that are not error conditions, but that may require special handling.     |
|  6  |Informational| `LOG_INFO`  |Informational messages           |                                                                                     |
|  7  |    Debug    | `LOG_DEBUG` |Debug-level messages             |Messages that contain information normally of use only when debugging a program.     |

The logger will print all messages with the current level and lower. The default log-level is `LOG_NOTICE`, the log-level can be modified using the `logger::set_log_level(unsigned)` function.

The compiler definition `-DBSLOG_NO_COLORS` will turn off colored output.
If one wants to modify the colors, simply replace the `BSLOG_COLOR_*` definitions in the header file.