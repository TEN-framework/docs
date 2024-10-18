# TEN Manager - Check Graph

tman provides the `check graph` command to check if the predefined graphs or a `start_graph` cmd are valid. Using the following command to see the usage.

{% code title=">_ Terminal" %}

```shell
tman check graph -h
```

{% endcode %}

The output will be as follows.

```text
Check whether the graph content of the predefined graph or start_graph command is correct. For more detailed usage, run 'graph -h'

Usage: tman check graph [OPTIONS] --app <APP>

Options:
      --app <APP>
          The absolute path of the app declared in the graph. By default, the predefined graph will be read from the first one in the list.
      --predefined-graph-name <PREDEFINED_GRAPH_NAME>
          Specify the predefined graph name only to be checked, otherwise, all predefined graphs will be checked.
      --graph <GRAPH>
          Specify the json string of a 'start_graph' cmd to be checked. If not specified, the predefined graph in the first app will be checked.
  -h, --help
          Print help
```

The `check graph` command is not expected to be running in a directory of any TEN app, so the `--app` is required.

Ex:

* Check all the predefined graphs in property.json.

  {% code title=">_ Terminal" %}

  ```shell
  tman check graph --app /home/TEN-Agent/agents
  ```
  
  {% endcode %}

* Check a predefined graph in property.json.

  {% code title=">_ Terminal" %}

  ```shell
  tman check graph --predefined-graph-name va.openai.azure --app /home/TEN-Agent/agents
  ```
  
  {% endcode %}

* Check a start_graph cmd.

  {% code title=">_ Terminal" %}

  ```shell
  tman check graph --graph '{
    "type": "start_graph",
    "seq_id": "55",
    "nodes": [
        {
        "type": "extension",
        "name": "test_extension",
        "addon": "basic_hello_world_2__test_extension",
        "extension_group": "test_extension_group",
        "app": "msgpack://127.0.0.1:8001/"
        },
        {
        "type": "extension",
        "name": "test_extension",
        "addon": "basic_hello_world_1__test_extension",
        "extension_group": "test_extension_group",
        "app": "msgpack://127.0.0.1:8001/"
        }
    ]
  }' --app /home/TEN-Agent/agents
  ```
  
  {% endcode %}

## Requirements

* The TEN apps specified by `--app` should have the configuration files -- `property.json` and `manifest.json`. If the predefined graphs are intended to be checked, they should be defined in the property.json.

* All packages used in the graphs must have been installed, i.e., the `tman install` should be executed in each app before running `check graph` command.

* If a multi-apps graph is being checked, each app should have a unique `_ten::uri` in property.json.

## Rules

### Nodes are required.

The `nodes` array is required in a graph.

* case 1

  {% code title=">_ Terminal" %}

  ```shell
  tman check graph --graph '{
    "type": "start_graph",
    "seq_id": "55",
    "nodes": [
    ]
  }' --app /home/TEN-Agent/agents
  ```

  {% endcode %}

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
    No extension node is defined in graph.

  All is done.
  💔  Error: 1/1 graphs failed.
  ```

### Each node should have a unique identity (i.e., app + extension_group + name).

* case 2

  {% code title=">_ Terminal" %}

  ```shell
  tman check graph --graph '{
    "type": "start_graph",
    "seq_id": "55",
    "nodes": [
      {
        "type": "extension",
        "name": "test_extension",
        "addon": "basic_hello_world_2__test_extension",
        "extension_group": "test_extension_group",
        "app": "msgpack://127.0.0.1:8001/"
      },
      {
        "type": "extension",
        "name": "test_extension",
        "addon": "basic_hello_world_1__test_extension",
        "extension_group": "test_extension_group",
        "app": "msgpack://127.0.0.1:8001/"
      }
    ]
  }' --app /home/TEN-Agent/agents
  ```

  {% endcode %}

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
    Duplicated extension was found in nodes[1], addon: basic_hello_world_1__test_extension, name: test_extension.

  All is done.
  💔  Error: 1/1 graphs failed.
  ```

### Extensions used in connections should be defined in nodes.

* case 3: the source extension is not defined.

  Imagine that the content of property.json of a TEN app is as follows.

  ```json
  {
    "_ten": {
      "predefined_graphs": [
        {
          "name": "default",
          "auto_start": false,
          "nodes": [
            {
              "type": "extension",
              "name": "some_extension",
              "addon": "default_extension_go",
              "extension_group": "some_group"
            }
          ],
          "connections": [
            {
              "extension": "some_extension",
              "extension_group": "producer",
              "cmd": [
                {
                  "name": "hello",
                  "dest": [
                    {
                      "extension_group": "some_group",
                      "extension": "some_extension"
                    }
                  ]
                }
              ]
            }
          ]
        }
      ]
    }
  }
  ```

  Checking graph with the following command.

  ```shell
  tman check graph --app /home/TEN-Agent/agents
  ```

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
    The extension declared in connections[0] is not defined in nodes, extension_group: producer, extension: some_extension.

  All is done.
  💔  Error: 1/1 graphs failed.
  ```

* case 4: the dest extension is not defined.

  Imagine that the content of property.json of a TEN app is as follows.

  ```json
  {
    "_ten": {
			"predefined_graphs": [
				{
					"name": "default",
					"auto_start": false,
					"nodes": [
						{
							"type": "extension",
							"name": "some_extension",
							"addon": "default_extension_go",
							"extension_group": "some_group"
						},
						{
							"type": "extension",
							"name": "some_extension_1",
							"addon": "default_extension_go",
							"extension_group": "some_group"
						}
					],
					"connections": [
						{
							"extension": "some_extension",
							"extension_group": "some_group",
							"cmd": [
								{
									"name": "hello",
									"dest": [
										{
											"extension_group": "some_group",
											"extension": "some_extension_1"
										}
									]
								},
								{
									"name": "world",
									"dest": [
										{
											"extension_group": "some_group",
											"extension": "some_extension_1"
										},
										{
											"extension_group": "some_group",
											"extension": "consumer"
										}
									]
								}
							]
						}
					]
				}
			]
    }
  }
  ```

  Checking graph with the following command.

  ```shell
  tman check graph --app /home/TEN-Agent/agents
  ```

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
		The extension declared in connections[0].cmd[1] is not defined in nodes, extension_group: some_group, extension: consumer.

	All is done.
	💔  Error: 1/1 graphs failed.
  ```

### The addons declared in the `nodes` must be installed in the app.

* case 5: the `_ten::uri` in property.json is not equal to the `app` field in nodes.

  Imagine that the content of property.json of a TEN app is as follows.

	```json
	{
		"_ten": {
			"predefined_graphs": [
				{
					"name": "default",
					"auto_start": false,
					"nodes": [
						{
							"type": "extension",
							"name": "some_extension",
							"addon": "default_extension_go",
							"extension_group": "some_group"
						}
					]
				}
			],
			"uri": "http://localhost:8001"
		}
	}
	```

	Checking graph with the following command.

  ```shell
  tman check graph --app /home/TEN-Agent/agents
  ```

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
		The following packages are declared in nodes but not installed: [("localhost", Extension, "default_extension_go")].

	All is done.
	💔  Error: 1/1 graphs failed.
  ```

	The problem is all packages in the app will be stored in a map which key is the `uri` of the app, and each node in the graph is retrieved by the `app` field (which is `localhost` by default). The `app` in node (i.e., localhost) is mismatch with the `uri` of app (i.e., http://localhost:8001).

* case 6: the ten_packages does not exist as the `tman install` has not executed.

  Imagine that the content of property.json of a TEN app is as follows.

	```json
	{
		"_ten": {
			"predefined_graphs": [
				{
					"name": "default",
					"auto_start": false,
					"nodes": [
						{
							"type": "extension",
							"name": "some_extension",
							"addon": "default_extension_go",
							"extension_group": "some_group"
						}
					]
				}
			]
		}
	}
	```

	And the `ten_packages` directory does **_NOT** exist.

	Checking graph with the following command.

  ```shell
  tman check graph --app /home/TEN-Agent/agents
  ```

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
		The following packages are declared in nodes but not installed: [("localhost", Extension, "default_extension_go")].

	All is done.
	💔  Error: 1/1 graphs failed.
  ```

### In connections, messages sent from one extension should be defined in the same section.

* case 7

  Imagine that all the packages have been installed.

	Checking graph with the following command.

  ```shell
  tman check graph --graph '{
		"nodes": [
			{
				"type": "extension",
				"name": "some_extension",
				"addon": "default_extension_go",
				"extension_group": "some_group"
			},
			{
				"type": "extension",
				"name": "another_ext",
				"addon": "default_extension_go",
				"extension_group": "some_group"
			}
		],
		"connections": [
			{
				"extension": "some_extension",
				"extension_group": "some_group",
				"cmd": [
					{
						"name": "hello",
						"dest": [
							{
								"extension_group": "some_group",
								"extension": "another_ext"
							}
						]
					}
				]
			},
			{
				"extension": "some_extension",
				"extension_group": "some_group",
				"cmd": [
					{
						"name": "hello_2",
						"dest": [
							{
								"extension_group": "some_group",
								"extension": "another_ext"
							}
						]
					}
				]
			}
		]
	}' --app /home/TEN-Agent/agents
  ```

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
		extension 'some_extension' is defined in connection[0] and connection[1], merge them into one section.

	All is done.
	💔  Error: 1/1 graphs failed.
  ```

### In connections, the messages sent out from one extension should have a unique name in each type.

* case 8

  Imagine that all the packages have been installed.

	Checking graph with the following command.

  ```shell
  tman check graph --graph '{
		"nodes": [
			{
				"type": "extension",
				"name": "some_extension",
				"addon": "addon_a",
				"extension_group": "some_group"
			},
			{
				"type": "extension",
				"name": "another_ext",
				"addon": "addon_b",
				"extension_group": "some_group"
			}
		],
		"connections": [
			{
				"extension": "some_extension",
				"extension_group": "some_group",
				"cmd": [
					{
						"name": "hello",
						"dest": [
							{
								"extension_group": "some_group",
								"extension": "another_ext"
							}
						]
					},
					{
						"name": "hello",
						"dest": [
							{
								"extension_group": "some_group",
								"extension": "some_extension"
							}
						]
					}
				]
			}
		]
	}' --app /home/TEN-Agent/agents
  ```

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
		- connection[0]:
			- Merge the following cmd into one section:
				'hello' is defined in flow[0] and flow[1].

	All is done.
	💔  Error: 1/1 graphs failed.
  ```

### The messages declared in the connections should be compatible.

The message declared in each message flow in the connections will be checked if the schema is compatible, according to the schema definition in the manifest.json of extensions. The rules of message compatible are as follows.

* If the message schema is not found neither in the source extension nor the target extension, the message is compatible.
* If the message schema is only found in one of the extensions, the message is incompatible.
* The message is compatible only if the following conditions are met.
  * The property type is compatible.
	* If the property is an object, the fields both in the source and target schema must have a compatible type.
	* If the `required` keyword is defined in the target schema, there must be a `required` keyword in the source schema, which is the superset of the `required` in the target.

* case 9

  Imagine that all the packages have been installed.

	The content of manifest.json in `addon_a` is as follows.

	```json
	{
		"type": "extension",
		"name": "addon_a",
		"version": "0.1.0",
		"dependencies": [
			{
				"type": "system",
				"name": "ten_runtime_go",
				"version": "0.1.0"
			}
		],
		"package": {
			"include": [
				"**"
			]
		},
		"api": {
			"cmd_out": [
				{
					"name": "cmd_1",
					"property": {
						"foo": {
							"type": "string"
						}
					}
				}
			]
		}
	}
	```

	And, the content of manifest.json in `addon_b` is as follows.

	```json
	{
		"type": "extension",
		"name": "addon_b",
		"version": "0.1.0",
		"dependencies": [
			{
				"type": "system",
				"name": "ten_runtime_go",
				"version": "0.1.0"
			}
		],
		"package": {
			"include": [
				"**"
			]
		},
		"api": {
			"cmd_in": [
				{
					"name": "cmd_1",
					"property": {
						"foo": {
							"type": "int8"
						}
					}
				}
			]
		}
	}
	```

	Checking graph with the following command.

  ```shell
  tman check graph --graph '{
		"nodes": [
			{
				"type": "extension",
				"name": "some_extension",
				"addon": "addon_a",
				"extension_group": "some_group"
			},
			{
				"type": "extension",
				"name": "another_ext",
				"addon": "addon_b",
				"extension_group": "some_group"
			}
		],
		"connections": [
			{
				"extension": "some_extension",
				"extension_group": "some_group",
				"cmd": [
					{
						"name": "cmd_1",
						"dest": [
							{
								"extension_group": "some_group",
								"extension": "another_ext"
							}
						]
					}
				]
			}
		]
	}' --app /home/TEN-Agent/agents
  ```

  The output will be as follows.

  ```text
  Checking graph[0]... ❌. Details:
  - connections[0]: 
    - cmd[0]:  Schema incompatible to [extension_group: some_group, extension: another_ext], properties are incompatible: 
  	    property [foo], Type is incompatible, source is [string], but target is [int8].

	All is done.
	💔  Error: 1/1 graphs failed.
  ```

