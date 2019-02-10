+++
date = "2019-02-09T12:00:00-00:00"
title = "Concurrency in Go"

+++

Multithreading is one of the most painful things that a programmer has to deal with. It often leads to race conditions and data corruption and all sorts of production problems. It's should be avoided at all costs, unless absolutely necessary. 

Luckly, it is not that hard when programming in Go. Such capabitily and complexity has been deeply considerate while developing the language and introduced as a first-class feature.


{{< youtube cN_DpYBzKso >}}



The challenge with multithreading is that we can't predict the code execution. The Go language introduces two key components to help address the complexity of concurrency, Goroutines and Channels.

A goroutine is a function that is capable of running concurrently with other functions. To create a goroutine we use the keyword go followed by a function invocation

{{< highlight go "hl_lines=19 20" >}}
package main

import (
  "fmt"
  "time"
)

func pinger(c chan string) {
	// Do Something
}

func printer(c chan string) {
	// Do Something
}

func main() {
  var c chan string = make(chan string)

  go pinger(c)
  go printer(c)

  var input string
  fmt.Scanln(&input)
}
{{< / highlight >}}


Channels provide a way for two goroutines to communicate with one another and synchronize their execution. Here is an example program using channels.

{{< highlight go "hl_lines=23" >}}
package main

import (
  "fmt"
  "time"
)

func pinger(c chan string) {
  for i := 0; ; i++ {
    c <- "ping"
  }
}

func printer(c chan string) {
  for {
    msg := <- c
    fmt.Println(msg)
    time.Sleep(time.Second * 1)
  }
}

func main() {
  var c chan string = make(chan string)

  go pinger(c)
  go printer(c)

  var input string
  fmt.Scanln(&input)
}
{{< / highlight >}}


Go has a special statement called select which works like a switch but for channels:


{{< highlight go "hl_lines=21 22-25">}}

func main() {
  c1 := make(chan string)
  c2 := make(chan string)

  go func() {
    for {
      c1 <- "from 1"
      time.Sleep(time.Second * 2)
    }
  }()

  go func() {
    for {
      c2 <- "from 2"
      time.Sleep(time.Second * 3)
    }
  }()

  go func() {
    for {
      select {
      case msg1 := <- c1:
        fmt.Println(msg1)
      case msg2 := <- c2:
        fmt.Println(msg2)
      }
    }
  }()

  var input string
  fmt.Scanln(&input)
}

{{< / highlight >}}

