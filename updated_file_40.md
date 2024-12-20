# 运维监控

> 此文档主要介绍 TuGraph 的可视化运维监控

## 1.设计思路

可视化监控并不是TuGraph自身不可或缺的一部分，因此在设计时将可视化监控作为TuGraph周边生态中的一个应用，来减少和TuGraph数据库的耦合度，以及对于TuGraph自身的影响。TuGraph可视化监控采用目前最火热的开源解决方案，TuGraph Monitor + Prometheus + Grafana来实现。其中TuGraph Monitor作为TuGraph服务的客户端，通过TCP链接向TuGraph服务发起Procedure请求，TuGraph服务在接收到请求后收集自身所在机器的cpu，memory，disk，io，以及请求数量等指标的统计结果进行响应。TuGraph Monitor在接收到TuGraph响应的指标数据后，将数据包装成prometheus需要的格式，保存在内存中，等待Prometheus服务通过http请求获取。Prometheus服务会定期通过http请求从TuGraph Monitor获取封装好的请求数据，按照获取的时间保存在自己的时序数据库中。Grafana可以根据用户的配置，从Prometheus处获取某个时间段内的统计数据，并在web界面上绘制浅显易懂的图形来展示最终结果。整个请求链路中，都采用了主动获取，即PULL的模型，好处之一是它能最大限度的避免数据生产者和数据消费者之间的耦合度，使得开发更简单，好处之二是数据生产者不需要考虑数据消费者的数据处理能力，即使某个消费者的数据处理能力较弱，也不会因为生产者生产数据过快而压垮消费者。主动拉取模型的不足之处在于数据的实时性不够，但在这个场景中，数据并没有很高的实时性要求。

### 1.1.TuGraph

TuGraph数据库提供了收集服务所在机器中磁盘，内存，网络IO，以及查询请求等多种数据信息的能力，并通过标准Procedure方式提供查询。收集数据这一动作仅在有用户通过接口查询时才会发生，避免了在用户不需要TuGraph监控服务所在机器的指标时对用户业务查询请求带来的影响。

### 1.2.TuGraph Monitor

TuGraph Monitor是TuGraph周边生态中的一个工具，它作为TuGraph众多用户中的一个，通过C++ RPC Client与TuGraph进行通信，通过Procedure查询接口来查询TuGraph服务所在机器的性能指标，并将TuGraph返回的结果包装成Prometheus需要的数据模型，等待Prometheus获取。用户可以通过设置查询时间间隔来保证获取监控指标对于业务查询的影响最小化。

### 1.3.Prometheus

Prometheus是一个开源的监控平台，并配备有专属的时序数据库，它会定期通过http请求从TuGraph Monitor服务获取统计指标，并保存在自己的时序数据库中。详细信息请参考官网: https://prometheus.io/docs/introduction/first_steps

### 1.4.Grafana

Grafana是一个开源的可视化和分析软件，它可以从包含Prometheus在内的多个数据源中获取数据，并且可以将时序数据库中的数据转换为精美图形和可视化效果的工具。具体信息请参考官网: https://grafana.com/docs/grafana/v7.5/getting-started/

## 2.部署方案

### 2.1.第一步

启动TuGraph服务，详细方法请参考文档: https://github.com/TuGraph-db/tugraph-db/blob/master/doc/zh-CN/1.guide/3.quick-start.md

### 2.2.第二步

启动TuGraph Monitor工具，启动命令如下：

```shell
./lgraph_monitor --server_host 127.0.0.1:9091 -u admin -p your_password \
			--monitor_host 127.0.0.1:9999  --sampling_interval_ms 1000
```

参数含义如下

```shell
Available command line options:
    --server_host       Host on which the tugraph rpc server runs.
                        Default=127.0.0.1:9091.
    -u, --user          DB username.
    -p, --password      DB password.
    --monitor_host      Host on which the monitor restful server runs.
                        Default=127.0.0.1:9999.
    --sampling_interval_ms
                        sampling interval in millisecond. Default=1.5e2.
    -h, --help          Print this help message. Default=0.
```

### 2.3.第三步

+ 下载符合您机器架构以及系统版本的Prometheus tar包，下载地址: https://prometheus.io/download/

+ 解压tar包，命令如下

```shell
tar -zxvf prometheus-2.37.5.linux-amd64.tar.gz
```

+ 修改配置文件prometheus.yml，新增如下配置，使其可以抓取TuGraph Monitor包装好的性能数据

```yaml
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "tugraph"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9111"]
```

+ 启动prometheus，具体的启动参数可以通过如下命令获取

```shell
./prometheus -h
```

+ 验证prometheus服务是否正常，可以通过web端登陆prometheus服务，查询监控指标resources_report是否已经获取到，能成功查询到数据则正确

### 2.4.第四步

+ 下载符合您机器架构以及系统版本的Grafana安装包，下载地址: https://grafana.com/grafana/download

+ 安装Grafana，细节请参考: https://grafana.com/docs/grafana/v7.5/installation/

+ 启动Grafana，细节请参考: https://grafana.com/docs/grafana/v7.5/installation/

+ 配置Grafana，首先在数据源设置中配置Prometheus的IP地址，配置完成后可以通过测试连接功能，验证是否成功连接数据源。然后，导入如下模版，并在页面中根据实际情况，修改正确的接口IP和端口。最后可以根据实际情况设置刷新时间和监控时间范围

```json
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": {
          "type": "grafana"
        },
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "target": {
          "limit": 100,
          "matchAny": false,
          "tags": [],
          "type": "dashboard"
        },
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "fiscalYearStartMonth": 0,
  "graphTooltip": 0,
  "id": 2,
  "links": [],
  "liveNow": false,
  "panels": [
    {
      "datasource": {
        "type": "prometheus"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            }
          },
          "mappings": [],
          "unit": "kbytes"
        },
        "overrides": [
          {
            "matcher": {
              "id": "byName",
              "options": "D {instance=\"localhost:7010\", job=\"TuGraph\", resouces_type=\"memory\", type=\"available\"}"
            },
            "properties": [
              {
                "id": "displayName",
                "value": "others"
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "D {__name__=\"resources_report\", instance=\"localhost:7010\", job=\"TuGraph\", resouces_type=\"memory\", type=\"available\"}"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-green",
                  "mode": "fixed"
                }
              },
              {
                "id": "displayName",
                "value": "others"
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "others"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-blue",
                  "mode": "fixed"
                }
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "graph_used"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-orange",
                  "mode": "fixed"
                }
              }
            ]
          }
        ]
      },
      "gridPos": {
        "h": 16,
        "w": 6,
        "x": 0,
        "y": 0
      },
      "id": 14,
      "options": {
        "displayLabels": [
          "name",
          "value"
        ],
        "legend": {
          "displayMode": "table",
          "placement": "bottom",
          "values": [
            "percent",
            "value"
          ]
        },
        "pieType": "pie",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "code",
          "expr": "resources_report{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"memory\",type=\"self\"}",
          "legendFormat": "{ {type} }",
          "range": true,
          "refId": "A"
        },
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "code",
          "expr": "resources_report{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"memory\",type=\"available\"}",
          "hide": false,
          "legendFormat": "{ {type} }",
          "range": true,
          "refId": "B"
        },
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "code",
          "expr": "resources_report{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"memory\",type=\"total\"}",
          "hide": true,
          "legendFormat": "{ {label_name} }",
          "range": true,
          "refId": "C"
        },
        {
          "datasource": {
            "type": "__expr__"
          },
          "expression": "$C -$A - $B",
          "hide": false,
          "refId": "D",
          "type": "math"
        }
      ],
      "title": "内存",
      "type": "piechart"
    },
    {
      "alert": {
        "alertRuleTags": {},
        "conditions": [
          {
            "evaluator": {
              "params": [
                1000
              ],
              "type": "gt"
            },
            "operator": {
              "type": "and"
            },
            "query": {
              "params": [
                "A",
                "5m",
                "now"
              ]
            },
            "reducer": {
              "params": [],
              "type": "avg"
            },
            "type": "query"
          }
        ],
        "executionErrorState": "alerting",
        "for": "5m",
        "frequency": "1m",
        "handler": 1,
        "message": "【生产图数据库Grafana】\n  QPS超过1000",
        "name": "请求统计 alert",
        "noDataState": "no_data",
        "notifications": []
      },
      "datasource": {
        "type": "prometheus"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 7,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "lineInterpolation": "smooth",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          },
          "unit": " "
        },
        "overrides": [
          {
            "matcher": {
              "id": "byName",
              "options": "write"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-blue",
                  "mode": "fixed"
                }
              }
            ]
          }
        ]
      },
      "gridPos": {
        "h": 16,
        "w": 12,
        "x": 6,
        "y": 0
      },
      "id": 4,
      "options": {
        "legend": {
          "calcs": [
            "min",
            "max",
            "mean",
            "last"
          ],
          "displayMode": "table",
          "placement": "bottom"
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "code",
          "expr": "{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"request\",type=~\"total|write\"}",
          "legendFormat": "{ {type} }",
          "range": true,
          "refId": "A"
        }
      ],
      "thresholds": [
        {
          "colorMode": "critical",
          "op": "gt",
          "value": 1000,
          "visible": true
        }
      ],
      "title": "请求统计",
      "type": "timeseries"
    },
    {
      "datasource": {
        "type": "prometheus"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            }
          },
          "mappings": [],
          "unit": "decbits"
        },
        "overrides": [
          {
            "matcher": {
              "id": "byName",
              "options": "graph_used"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-red",
                  "mode": "fixed"
                }
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "available"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-orange",
                  "mode": "fixed"
                }
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "D"
            },
            "properties": [
              {
                "id": "displayName",
                "value": "other"
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "other"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-purple",
                  "mode": "fixed"
                }
              }
            ]
          }
        ]
      },
      "gridPos": {
        "h": 16,
        "w": 6,
        "x": 18,
        "y": 0
      },
      "id": 12,
      "options": {
        "displayLabels": [
          "name",
          "value"
        ],
        "legend": {
          "displayMode": "table",
          "placement": "bottom",
          "sortBy": "Value",
          "sortDesc": true,
          "values": [
            "value",
            "percent"
          ]
        },
        "pieType": "pie",
        "reduceOptions": {
          "calcs": [
            "lastNotNull"
          ],
          "fields": "",
          "values": false
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "code",
          "exemplar": false,
          "expr": "resources_report{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"disk\",type=\"available\"}",
          "format": "time_series",
          "instant": false,
          "interval": "",
          "legendFormat": "{ {type} }",
          "range": true,
          "refId": "A"
        },
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "code",
          "expr": "resources_report{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"disk\",type=\"self\"}",
          "hide": false,
          "legendFormat": "{ {type} }",
          "range": true,
          "refId": "B"
        },
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "code",
          "expr": "resources_report{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"disk\",type=\"total\"}",
          "hide": true,
          "legendFormat": "{ {type} }",
          "range": true,
          "refId": "C"
        },
        {
          "datasource": {
            "type": "__expr__"
          },
          "expression": "$C - $A - $B",
          "hide": false,
          "refId": "D",
          "type": "math"
        }
      ],
      "title": "磁盘",
      "transformations": [
        {
          "id": "configFromData",
          "options": {
            "applyTo": {
              "id": "byFrameRefID"
            },
            "configRefId": "config",
            "mappings": []
          }
        }
      ],
      "type": "piechart"
    },
    {
      "alert": {
        "alertRuleTags": {},
        "conditions": [
          {
            "evaluator": {
              "params": [
                90
              ],
              "type": "gt"
            },
            "operator": {
              "type": "and"
            },
            "query": {
              "params": [
                "A",
                "5m",
                "now"
              ]
            },
            "reducer": {
              "params": [],
              "type": "avg"
            },
            "type": "query"
          }
        ],
        "executionErrorState": "alerting",
        "for": "5m",
        "frequency": "1m",
        "handler": 1,
        "message": "【生产图数据库Grafana】\nCPU使用率超过90%",
        "name": "CPU使用率 alert",
        "noDataState": "no_data",
        "notifications": [
          {
          }
        ]
      },
      "datasource": {
        "type": "prometheus"
      },
      "description": "",
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 4,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "lineInterpolation": "linear",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          },
          "unit": "percent"
        },
        "overrides": [
          {
            "matcher": {
              "id": "byName",
              "options": "graph_used"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-orange",
                  "mode": "fixed"
                }
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "total_used"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-purple",
                  "mode": "fixed"
                }
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "self"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-green",
                  "mode": "fixed"
                }
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "total"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "light-purple",
                  "mode": "fixed"
                }
              }
            ]
          }
        ]
      },
      "gridPos": {
        "h": 14,
        "w": 12,
        "x": 0,
        "y": 16
      },
      "id": 6,
      "options": {
        "legend": {
          "calcs": [
            "min",
            "max",
            "mean",
            "last"
          ],
          "displayMode": "table",
          "placement": "bottom"
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "code",
          "expr": "resources_report{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"cpu\",type=~\"total|self\"}",
          "hide": false,
          "legendFormat": "{ {type} }",
          "range": true,
          "refId": "A"
        }
      ],
      "thresholds": [
        {
          "colorMode": "critical",
          "op": "gt",
          "value": 90,
          "visible": true
        }
      ],
      "title": "CPU使用率",
      "type": "timeseries"
    },
    {
      "alert": {
        "alertRuleTags": {},
        "conditions": [
          {
            "evaluator": {
              "params": [
                10000
              ],
              "type": "gt"
            },
            "operator": {
              "type": "and"
            },
            "query": {
              "params": [
                "A",
                "5m",
                "now"
              ]
            },
            "reducer": {
              "params": [],
              "type": "avg"
            },
            "type": "query"
          }
        ],
        "executionErrorState": "alerting",
        "for": "5m",
        "frequency": "1m",
        "handler": 1,
        "message": "【生产图数据库Grafana】\n  磁盘IO超过10MB/S",
        "name": "磁盘IO alert",
        "noDataState": "no_data",
        "notifications": []
      },
      "datasource": {
        "type": "prometheus"
      },
      "fieldConfig": {
        "defaults": {
          "color": {
            "mode": "palette-classic"
          },
          "custom": {
            "axisLabel": "",
            "axisPlacement": "auto",
            "barAlignment": 0,
            "drawStyle": "line",
            "fillOpacity": 7,
            "gradientMode": "none",
            "hideFrom": {
              "legend": false,
              "tooltip": false,
              "viz": false
            },
            "lineInterpolation": "smooth",
            "lineWidth": 1,
            "pointSize": 5,
            "scaleDistribution": {
              "type": "linear"
            },
            "showPoints": "auto",
            "spanNulls": false,
            "stacking": {
              "group": "A",
              "mode": "none"
            },
            "thresholdsStyle": {
              "mode": "off"
            }
          },
          "mappings": [],
          "thresholds": {
            "mode": "absolute",
            "steps": [
              {
                "color": "green",
                "value": null
              },
              {
                "color": "red",
                "value": 80
              }
            ]
          },
          "unit": "bps"
        },
        "overrides": [
          {
            "matcher": {
              "id": "byName",
              "options": "read"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "super-light-green",
                  "mode": "fixed"
                }
              }
            ]
          },
          {
            "matcher": {
              "id": "byName",
              "options": "write"
            },
            "properties": [
              {
                "id": "color",
                "value": {
                  "fixedColor": "super-light-red",
                  "mode": "fixed"
                }
              }
            ]
          }
        ]
      },
      "gridPos": {
        "h": 14,
        "w": 12,
        "x": 12,
        "y": 16
      },
      "id": 2,
      "options": {
        "legend": {
          "calcs": [
            "min",
            "max",
            "mean",
            "last"
          ],
          "displayMode": "table",
          "placement": "bottom"
        },
        "tooltip": {
          "mode": "single",
          "sort": "none"
        }
      },
      "targets": [
        {
          "datasource": {
            "type": "prometheus"
          },
          "editorMode": "builder",
          "expr": "resources_report{instance=\"localhost:7010\",job=\"TuGraph\",resouces_type=\"disk_rate\",type=~\"read|write\"}",
          "hide": false,
          "legendFormat": "{ {type} }",
          "range": true,
          "refId": "A"
        }
      ],
      "thresholds": [
        {
          "colorMode": "critical",
          "op": "gt",
          "value": 10000,
          "visible": true
        }
      ],
      "title": "磁盘IO",
      "type": "timeseries"
    }
  ],
  "refresh": "",
  "schemaVersion": 36,
  "style": "dark",
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-24h",
    "to": "now"
  },
  "timepicker": {
    "hidden": false,
    "refresh_intervals": [
      "10s"
    ]
  },
  "timezone": "",
  "title": "TuGraph监控页面",
  "version": 20,
  "weekStart": ""
}
```

验证效果，刷新浏览器页面。如果正确显示饼图和折线图，则配置完成。

## 3.未来计划

目前可视化监控只支持单机监控，能监控服务所在机器的cpu，磁盘，网络io，请求qps等性能指标，未来将会实现监控ha集群的功能，也会将更多有意义的指标纳入监控范围

