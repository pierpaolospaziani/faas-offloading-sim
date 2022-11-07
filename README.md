This software simulates the execution of a FaaS system deployed over Edge and 
Cloud nodes.
Currently, a single Edge node and a single Cloud node are considered (though
the Cloud node can actually represent an abstraction of multiple servers).


## Requirements

The simulator is written in Python.

You can optionally run it within a virtual environment:

	python -m venv env
	source env/bin/activate
	pip install -r requirements.txt 

## Usage

A minimal run can be triggered as follows:

	python main.py [<config file>]

If no configuration file is specified, `./config.ini` is used.

## Configuration

Example configuration:

	[seed]
	arrival = 1
	service = 5

	[simulation]
	stats-print-interval = 120
	stats-print-file = stats.txt
	close-door-time = 10000

	[cloud]
	speedup = 1.1
	cost = 0.00001

	[edge]
	speedup = 1.0
	cloud-latency = 0.050

	[policy]
	name = probabilistic
	update-interval = 120
	arrival-alpha = 0.3

	[container]
	init-time = 1.1

	[fun_f1]
	arrival-rate = 30
	service-time-mean = 0.242
	service-time-scv = 0.25
	memory = 1024

	[fun_f2]
	arrival-rate = 30
	service-time-mean = 0.045
	service-time-scv = 0.25
	memory = 512

	[class_default]
	arrival-weight = 1.0
	deadline = 1.0
	utility = 1.0

	[class_premium]
	arrival-weight = 1.0
	deadline = 0.5
	utility = 5.0


As seen above, the configuration file can be used to define an arbitrary number
of functions and service classes.

Other example of configuration files are available in `examples`.

### Arrival traces

Each function is associated with an arrival process. Incoming requests
are randomly associated with a QoS class based on the `arrival-weight` attribute
of each class (i.e., the higher the weight, the higher the probability of
belonging to the class). 

You can either specify:

- a constant average `arrival-rate` for the function: arrivals will follow a
Poisson process.
- an inter-arrival times (IATs) trace file, using the `arrival-trace` attribute. The
  trace is a text file containing IATs, one per line.

### Policies

The following policies are currently implemented:

- `basic`: greedy heuristic that schedules all the requests for
  local execution when possible
- `probabilistic`: probabilistic heuristic, whose probabilities are periodically
  updated solving a LP problem
- `probabilistic-legacy`: legacy version of the aforementioned LP-based approach
- `random`
