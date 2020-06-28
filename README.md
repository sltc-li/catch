catch
=====

This repository demonstrates an example of how error handling may change with [Go generics](https://go.googlesource.com/proposal/+/refs/heads/master/design/go2draft-type-parameters.md).

### Without catch
```go
var getString func() (string, error)

var validate func(i int) error

func f() (int, error) {
    s, err := getString()
    if err != nil {
        return 0, fmt.Errorf("get string: %w", err)
    }

    i, err := strconv.Atoi(s)
    if err != nil {
        return 0, fmt.Errorf("convert integer: %w", err)
    }

    if err := validate(i); err != nil {
        return 0, fmt.Errorf("validate: %w", err)
    }

    return i, nil
}
```

### With catch
```go
var getString func() (string, error)

var validate func(i int) error

func f() (_ int, err error) {
    defer catch.Catch(&err)

    s := catch.Try(getString()).Wrap("get string").Must()
    i := catch.Try(strconv.Atoi(s)).Wrap("convert integer").Must()
    catch.DoAndWrap(validate(i), "validate")

    return i, nil
}
```
