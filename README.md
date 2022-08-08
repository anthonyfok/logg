
[![Tests on Linux, MacOS and Windows](https://github.com/bep/log/workflows/Test/badge.svg)](https://github.com/bep/log/actions?query=workflow:Test)
[![Go Report Card](https://goreportcard.com/badge/github.com/bep/log)](https://goreportcard.com/report/github.com/bep/log)
[![GoDoc](https://godoc.org/github.com/bep/log?status.svg)](https://godoc.org/github.com/bep/log)

This is a fork of the exellent [Apex Log](https://github.com/apex/log) library.

Main changes:

* Trim unneeded dependencies.
* Make `Fields` into a slice to preserve log order.
* Split `Entry` into `Entry` and `EntryFields`. This is easier to reason about and more effective.
* Split the old `Interface` in two and remove all but one `Log` method (see below).
* This allows for lazy creation of messages in `Log(fmt.Stringer)` and ignoring fields added in `LevelLogger`s with levels below the `Logger`'s.


```go
// Logger is the main interface for the logger.
type Logger interface {
	// WithLevel returns a new entry with `level` set.
	WithLevel(Level) *EntryFields
}

// LevelLogger handles logging for a given level.
type LevelLogger interface {
	// Log logs a message at the given level using the string from calling s.String().
	// Note that s.String() will not be called if the level is not enabled.
	Log(s fmt.Stringer)

	// WithLevel returns a new entry with `level` set.
	WithLevel(Level) *EntryFields

	// WithFields returns a new entry with the`fields` in fields set.
	// This is a noop if LevelLogger's level is less than Logger's.
	WithFields(fields Fielder) *EntryFields

	// WithLevel returns a new entry with the field f set with value v
	// This is a noop if LevelLogger's level is less than Logger's.
	WithField(f string, v any) *EntryFields

	// WithDuration returns a new entry with the "duration" field set
	// to the given duration in milliseconds.
	// This is a noop if LevelLogger's level is less than Logger's.
	WithDuration(time.Duration) *EntryFields

	// WithError returns a new entry with the "error" set to `err`.
	// This is a noop if err is nil or  LevelLogger's level is less than Logger's.
	WithError(error) *EntryFields
}

```