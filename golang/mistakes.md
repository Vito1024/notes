# Some mistakes in Go

- "Do you want to know the difference between a master and a beginner?
- The master has failed more times than the beginner has tried."


---
## Mistake 1. Not Accepting Interfaces.
> State & Behavior
- Types can express state & behavior
- State = data structure
- Behavior = methods

> Interface In Go: **Behavior**
- One of Go's most powerful features
- Permits extensibility
- Defined by methods
- Adherence is only satisified by hehavior

```go
// Stop Doing This!!!
func (page *Page) saveSourceAs(path string) {
    b := new(bytes.Buffer)
    b.Write(page.Source.Content)
    page.saveSource(b.Bytes(), path)
}

func (page *Page) saveSource(by []byte, inpath string) {
    writeToDisk(inpath, bytes.NewReader(by))
}

// Instead
func (page *Page) saveSourceAs(path string) {
    b := new(bytes.Buffer)
    b.Write(page.Source.Content)
    page.saveSource(b, path)
}

func (page *Page) saveSource(in io.Reader, inpath string) {
    writeToDisk(inpath. in)
}
```

---
## Mistake 2. Not using io.Reader & io.Writer
```go
type Reader interface {
    Read(p []byte) (n int, err error)
} 

type Writer interface {
    Write(p []byte) (n int, err error)
}
```
--- examples

```go
// If output is nil, os.Stdout is used.
func (c *Command) SetOutput(o io.Writer) {
    c.output = o
}

// Not this way
func (v *Viper) ReadBufConfig(buf *bytes.Buffer) error {
    // ***
}
// Instead
func (v *Viper) ReadConfig(in io.Reader) error {
    // ***
}
```

---
## Mistake 3. Requiring Broad Interfaces
> Interfaces Are Composable
- Functions should ***only accept*** interfaces that require the ***methods they need***
- Functions should not accept a broad interface when a narrow one would work
- Compose broad interfaces made from narrower ones

```go
//Composing Interfaces
type File interface {
    io.Closer
    io.Reader
    io.ReaderAt
    io.Seeker
    io.Writer
    io.WriterAt
}

// Bad Usage: Requiring Broad Interfaces
func ReadIn(f File) {
    b := []byte{}
    n, err := f.Read(b)
    ...
}
```

---
## Mistake 4. Methods Vs Functions
> Too Many Methods
- a lot of people from OO backgrounds ***overuse methods***

- ***Natural draw*** to define everything via structs and methods

> What Is A Function ?
- Operations performed on ***N1 inputs*** that results in ***N2 outputs***

- The ***same inputs*** will always result in the ***same outputs***

- Functions ***should not*** depend on state

> What Is A Method?
- Defines the ***behavior*** of a type

- A function that operates ***against a value***

- Should use ***state***

- Logically connected

- Usually change ***state***

> Functions Can Be Used With Interfaces
- Methods, by definition, are ***bound*** to a specific type

- Functions can ***accept interfaces*** as input

```go
func extractShortcodes(s string, p *Pages, t Template) (string, map[string]shortcode, error) {
    ...
    for {
        ...
        err := fmt.Error("%s:%d: %s",
            p.BaseFileName(),
            (p.lineNumRawContentStart()+
            pt.level.lineNum() - 1,
            currItem)
        )
    }
}
```

---
## Mistake 5. Pointers Vs Values
- It's ***not*** a question of ***performance***(generally), but one of ***shared access***

- If you want to ***share*** the value with a function or method, then ***use a pointer***

- If you ***don't want*** to ***share*** it, then use ***a value***(copy)

> Pointer receivers
- If you want to share a value with it's method, use a ***pointer receiver***

- Since methods commonly manage state, this is the ***common usage***

- ***Not safe*** for concurrent access

> Value receivers
- If you want the ***value copied***(not shared), use values

- If the type is an empty struct(stateless, just behavior)... then just use value

- ***Safe*** for concurrent access

---

## Mistake 6. Thinking Of Errors As Strings

> Error Is An Interface
```go
type error interface {
    Error() string
}
```

> Standard Errors
- ***errors.New("error here")*** is usually sufficient

- Exported Error Variables can be ***easily checked***
```go
// Not this way
func NewPage(name string) (*Page, error) {
    if len(name) == 0 {
        return nil, errors.New("Zero length page name")
    }
    ...
}

// Instead, exported error variable is easily checked
var ErrNoName = errors.New("Zero length page name")

func NewPage(name string) (*Page, error) {
    if len(name) == 0 {
        return nil, ErrNoName
    }
    ...
}

func AnotherFunc() {
    page, err := NewPage("page")
    if err == ErrNoName {
        ...
    }
    ...
}

```

---
## Mistake 7. To Be Safe Or Not To Be
You can't make everyone happy.

You are not a Jar Of Nutella.


> Consider Concurrency
- If you provide a library someone will use it ***concurrently***

- Data structures are ***not safe*** for concurrent access

- Values aren't safe, you need to ***create safe behavior*** around them

> How to make it safe
- ***sync package*** provides behavior to make a value safe(Atomic/Mutex)

- ***Channel*** coordinate values across go routines by permitting one go routine to access at a time


---
# The biggest mistake is Not Making Mistakes