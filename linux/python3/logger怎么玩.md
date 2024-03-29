python2.7 简单的配置：
```
import logging.config

LOGGING_CONFIG = {
    "version": 1,
    "formatters": {
        "default": {
            'format': '%(asctime)s %(filename)s %(lineno)s [%(levelname)s] %(message)s',
        },
        "plain": {
            "format": "%(message)s",
        },
    },
    "handlers": {
        "file": {
            "class": "logging.FileHandler",
            "level": "DEBUG",
            "filename": "./searchservice_api/log/invalid_request.txt",
            "formatter": "default",
        }
    },
    "loggers": {
        "file_logger": {
            "handlers": ["file"],
            "level": "DEBUG",
            "propagate": True,
        }
    },
    "disable_existing_loggers": False,
}

logging.config.dictConfig(LOGGING_CONFIG)
invalid_request_logger = logging.getLogger("file_logger")

invalid_request_logger.info("invalid_request_logger init success")
```

propagate:
```
传播，If this evaluates to true, events logged to this logger will be passed to the handlers of higher level (ancestor) loggers, in addition to any handlers attached to this logger. 

我个人理解就是比如你自己的 logger 挂了某个 handler，然后 root 的 logger 挂了 3 个 handler，这个配置打开后，相当于自己的 logger 挂了 4 个 handler。
```

disable_existing_loggers:
```
把其他的 logger 干掉, 默认配置中的logger全部被禁用。被禁用的logger并不是被删除了,它们仍然存在,只是静默地丢弃所有发来的日志请求.
```

上面的配置仅仅是打日志，还有类似的更多的配置：
```
from logging.config import dictConfig

LOGGING_CONFIG = {
    'version': 1,
    'loggers': {
        '': {  # root logger
            'level': 'NOTSET',
            'handlers': ['debug_console_handler', 'info_rotating_file_handler', 'error_file_handler', 'critical_mail_handler'],
        },
        'my.package': { 
            'level': 'WARNING',
            'propagate': False,
            'handlers': ['info_rotating_file_handler', 'error_file_handler' ],
        },
    },
    'handlers': {
        'debug_console_handler': {
            'level': 'DEBUG',
            'formatter': 'info',
            'class': 'logging.StreamHandler',
            'stream': 'ext://sys.stdout',
        },
        'info_rotating_file_handler': {
            'level': 'INFO',
            'formatter': 'info',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': 'info.log',
            'mode': 'a',
            'maxBytes': 1048576,
            'backupCount': 10
        },
        'error_file_handler': {
            'level': 'WARNING',
            'formatter': 'error',
            'class': 'logging.FileHandler',
            'filename': 'error.log',
            'mode': 'a',
        },
        'critical_mail_handler': {
            'level': 'CRITICAL',
            'formatter': 'error',
            'class': 'logging.handlers.SMTPHandler',
            'mailhost' : 'localhost',
            'fromaddr': 'monitoring@domain.com',
            'toaddrs': ['dev@domain.com', 'qa@domain.com'],
            'subject': 'Critical error with application name'
        }
    },
    'formatters': {
        'info': {
            'format': '%(asctime)s-%(levelname)s-%(name)s::%(module)s|%(lineno)s:: %(message)s'
        },
        'error': {
            'format': '%(asctime)s-%(levelname)s-%(name)s-%(process)d::%(module)s|%(lineno)s:: %(message)s'
        },
    },

}

dictConfig(LOGGING_CONFIG)
```
