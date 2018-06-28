automaton
========

.. _automaton: https://github.com/openstack/automaton 
automaton_ 是一个状态转换机库。OpenStack 的 taksflow 库就是基于 automaton 实现的。  

在状态转换机中比较常见的是`有限状态机`，理解起来就是定义一个系统的几个状态，给出各个状态之间装换的条件，然后状态加触发条件就决定了系统的下一个状态。我们还可以在状态转换的各个阶段插入自己想要实现的逻辑。

因此，一个有限状态机中可以提取出几个重要的元素：  

*  状态列表，系统可能存在的状态
*  转换条件，状态转换的条件
*  钩子函数，状态转换前后调用函数  

一个最简单的有限状态机定义如下：  

.. code-block:: python

	from automaton import machines

	m = machines.FiniteMachine()  #FiniteMachine 有限状态机
	m.add_state('up')
	m.add_state('down')
	m.add_transition('down', 'up', 'jump')
	m.add_transition('up', 'down', 'fall')
	m.defualt_start_state = 'down'
	print(m.pformat())

	# output:
	# +-------+-------+------+----------+---------+
	# | Start | Event | End  | On Enter | On Exit |
	# +-------+-------+------+----------+---------+
	# |  down |  jump |  up  |    .     |    .    |
	# |   up  |  fall | down |    .     |    .    |
	# +-------+-------+------+----------+---------+

当要使用状态机时，需要先初始化状态机，然后可以调用 process_event 来触发转换条件：  


.. code-block:: python

	from automaton import machines

	m = machines.FiniteMachine()

	m.add_state('up')
	m.add_state('down')
	m.add_transition('down', 'up', 'jump')
	m.add_transition('up', 'down', 'fall')
	m.default_start_state = 'down'

	m.initialize()  # 初始化状态机
	m.process_event('jump')
	print(m.pformat())
		# +---------+-------+------+----------+---------+
		# |  Start  | Event | End  | On Enter | On Exit |
		# +---------+-------+------+----------+---------+
		# | down[^] |  jump |  up  |    .     |    .    |
		# |   @up   |  fall | down |    .     |    .    |
		# +---------+-------+------+----------+---------+	
	print(m.current_state)  # up
	print(m.terminated)     # False
	m.process_event('fall') 
	print(m.pformat())
		# +----------+-------+------+----------+---------+
		# |  Start   | Event | End  | On Enter | On Exit |
		# +----------+-------+------+----------+---------+
		# | @down[^] |  jump |  up  |    .     |    .    |
		# |    up    |  fall | down |    .     |    .    |
		# +----------+-------+------+----------+---------+
	print(m.current_state) # down
	print(m.terminated)    # False

在 add_state 中还可以添加另外两个参数，分别表示状态变化前后的钩子函数。  

.. code-blcok:: python

	def print_on_enter(new_state, triggered_event):
		....

	def print_on_exit(old_state, triggered_event):
		...
	...

	# on_enter 的函数传入的参数是: new_state， 触发条件
	# on_exit 的函数传入的参数是: old_state, 触发条件	
	m.add_state('up', on_enter=print_on_enter, on_exit=print_on_exit)
	...

除了可以使用 add_state、add_transition 等定义状态机之外，还可以使用一个 json 列表来定义状态机，然后将 json 传递给初始化函数进行状态机的初始化：  

.. code-block:: python

	from automaton import machines


	def print_on_enter(new_state, triggered_event):
	    print("Enter '%s' due to '%s'" % (new_state, triggered_event))

	def print_on_exit(old_state, triggered_event):
	    print("Exiting '%s' due to '%s'" % (old_state, triggered_event))

	state_space = [
	        {
	            'name': 'up',
	            'next_states': {
	                'fall': 'down'
	            },
	            'on_enter': print_on_enter,
	            'on_exit': print_on_exit
	        },
	        {
	            'name': 'down',
	            'next_states': {
	                'jump':'up'
	            },
	            'on_enter': print_on_enter,
	            'on_exit': print_on_exit
	        }
	]

	m = machines.FiniteMachine.build(state_space)
	m.default_start_state = 'up'
	print m.pformat()
		#  ^ 表示初始状态 @ 表示当前状态
		# +-------+-------+------+-------------------------+------------------------+
		# | Start | Event | End  |         On Enter        |        On Exit         |
		# +-------+-------+------+-------------------------+------------------------+
		# |  down |  jump |  up  | __main__.print_on_enter | __main__.print_on_exit |
		# | up[^] |  fall | down | __main__.print_on_enter | __main__.print_on_exit |
		# +-------+-------+------+-------------------------+------------------------+

	m.initialize()

	m.process_event('fall')
		# Exiting 'up' due to 'fall'
		# Enter 'down' due to 'fall'
	print m.pformat()
		# +-------+-------+------+-------------------------+------------------------+
		# | Start | Event | End  |         On Enter        |        On Exit         |
		# +-------+-------+------+-------------------------+------------------------+
		# | @down |  jump |  up  | __main__.print_on_enter | __main__.print_on_exit |
		# | up[^] |  fall | down | __main__.print_on_enter | __main__.print_on_exit |
		# +-------+-------+------+-------------------------+------------------------+

	m.process_event('jump')
		# Exiting 'down' due to 'jump'
		# Enter 'up' due to 'jump'

	print m.pformat()
		# +--------+-------+------+-------------------------+------------------------+
		# | Start  | Event | End  |         On Enter        |        On Exit         |
		# +--------+-------+------+-------------------------+------------------------+
		# |  down  |  jump |  up  | __main__.print_on_enter | __main__.print_on_exit |
		# | @up[^] |  fall | down | __main__.print_on_enter | __main__.print_on_exit |
		# +--------+-------+------+-------------------------+------------------------+

