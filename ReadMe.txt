1) Установил ClickHouse, создал таблицу и загрузил в нее тестовый датасет Trips
2) Добавил две реплики, собрал кластер ZooKeeper
	-Конфигурация для реплик
<clickhouse>
        <remote_servers>
                <cluster>
                        <shard>
                            <replica>
                                <host>192.168.50.118</host>
                                <port>9000</port>
                            </replica>
                            <replica>
                                <host>192.168.50.119</host>
                                <port>9000</port>
                            </replica>
                            <replica>
                                <host>192.168.50.125</host>
                                <port>9000</port>
                            </replica>
                        </shard>
                    </cluster>
</clickhouse>
        </remote_servers>

	-Макрос
<clickhouse>
        <macros>            
            <replica>clickhouse02.home.local</replica>            
        </macros>
</clickhouse>

	-Кластер ZooKeeper
<clickhouse>
        <zookeeper>
            <node index="1">
                <host>192.168.50.100</host>
                <port>2181</port>
            </node>
            <node index="2">
                <host>192.168.50.101</host>
                <port>2181</port>
            </node>
            <node index="3">
                <host>192.168.50.150</host>
                <port>2181</port>
            </node>
        </zookeeper>
</clickhouse>

3) Преобразовал таблицу в Replicated
4) Получил все данные на каждой реплике
5) Добавил два шарда, у каждого шарда по две реплики + 1 реплика для исходного шарда. На выходе получил топологию: старый кластер: три реплики, новый кластер: три шарда, у каждого по две реплики
6) Внес изменения в конфигурацию кластера
<clickhouse>
        <remote_servers>
                <cluster_click_new>
                        <shard>
                            <replica>
                                <host>192.168.50.118</host>
                                <port>9000</port>
                            </replica>
                            <replica>
                                <host>192.168.50.130</host>
                                <port>9000</port>
                            </replica>
                        </shard>
                        <shard>
                            <replica>
                                <host>192.168.50.126</host>
                                <port>9000</port>
                            </replica>
                            <replica>
                                <host>192.168.50.127</host>
                                <port>9000</port>
                            </replica>
                        </shard>
                        <shard>
                            <replica>
                                <host>192.168.50.128</host>
                                <port>9000</port>
                            </replica>
                            <replica>
                                <host>192.168.50.129</host>
                                <port>9000</port>
                            </replica>
                        </shard>
                    </cluster_click>
        </remote_servers>
</clickhouse>

7) На каждом шарде создал реплицируемую таблицу
8) На кластере создал Distributed таблицу
9) Перегрузил в нее данные, при обращении к Distributed таблице получаю все данные, при обращении к локальным таблицам шардов получаю только локальные данные.
