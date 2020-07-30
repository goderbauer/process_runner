# Process

The [`process_runner`] package for Dart uses the [`ProcessManager`] class from
[`process`] package to allow invocation of external OS processes, and manages
the stderr and stdout properly so that you don't lose any output, and can easily
access it without needing to wait on streams.

Like `dart:io` and [`process`], it supplies a rich, Dart-idiomatic API for
spawning OS processes, with the added benefit of easy retrieval of stdout and
stderr from the result of running the process, with proper waiting for the
process and stderr/stdout streams to be closed. Because it uses [`process`], you
can supply a mocked [`ProcessManager`] to allow testing of code that uses
[`process_runner`].

In addition to being able to launch processes separately with [`ProcessRunner`],
it allows creation of a pool of worker processes with [`ProcessPool`], and
manages running them with a set number of active [`WorkerJob`s], and manages the
collection of their stdout, stderr, and interleaved stdout and stderr output.

See the [example](example/main.dart) and [`process_runner` library] docs for
more information on how to use it, but the basic usage for  is:

```dart
import 'package:process_runner/process_runner.dart';

void main() {
  ProcessRunnerResult result = await processRunner.runProcess(['command', 'arg1', 'arg2']);
  // Print stdout:
  print(result.stdout);
  // Print stderr:
  print(result.stderr);
  // Print interleaved stdout/stderr:
  print(result.output);
}
```

For the [`ProcessPool`](lib/process_pool.dart), also see the [example](example),
but it basically looks like this:

```dart
  ProcessPool pool = ProcessPool(numWorkers: 2);
  final List<WorkerJob> jobs = <WorkerJob>[
    WorkerJob('Job 1', ['command1', 'arg1', 'arg2']),
    WorkerJob('Job 2', ['command2', 'arg1']),
  ];
  await for (final WorkerJob job in pool.startWorkers(jobs)) {
    print('\nFinished job ${job.name}');
  }
```

Or, if you just want the answer when it's done:

```dart
  ProcessPool pool = ProcessPool(numWorkers: 2);
  final List<WorkerJob> jobs = <WorkerJob>[
    WorkerJob('Job 1', ['command1', 'arg1', 'arg2']),
    WorkerJob('Job 2', ['command2', 'arg1']),
  ];
  List<WorkerJob> finishedJobs = pool.runToCompletion(jobs);
```


[`ProcessManager`]: https://github.com/google/process.dart/blob/master/lib/src/interface/process_manager.dart#L21
[`process`]: https://pub.dev/packages/process
[`process_runner`]: https://pub.dev/packages/process_runner
[`ProcessRunner`]: https://pub.dev/documentation/process_runner/latest/process_runner/ProcessRunner-class.html
[`ProcessPool`]: https://pub.dev/documentation/process_runner/latest/process_runner/ProcessPool-class.html
[`process_runner` library]: https://pub.dev/documentation/process_runner/latest/process_runner/process_runner-library.html
[`WorkerJob`s]: https://pub.dev/documentation/process_runner/latest/process_runner/WorkerJob-class.html
