import socket
import time


class ClientError(Exception):
    pass


class Client:
    def __init__(self, host, port, timeout=None):
        self.host = host
        self.port = port
        self.timeout = timeout or 0
        self.sock = socket.create_connection((host, port), timeout=timeout)

    def put(self, key, value, timestamp=None):
        timestamp = timestamp or str(int(time.time()))
        self.sock.sendall(('put ' + key + ' ' + str(value) + ' ' + str(timestamp) + '\n').encode('utf8'))
        data = self.sock.recv(1024).decode('utf8')
        if data == 'error\nwrong command\n\n':
            raise ClientError
        else:
            pass

    def get(self, key):
        metrics = {}
        tuples = []
        self.sock.sendall(('get ' + key + '\n').encode('utf8'))
        data = self.sock.recv(1024).decode('utf8')
        if data == 'ok\n\n':
            return metrics
        elif data == 'error\nwrong command\n\n':
            raise ClientError
        else:
            for i in data.split('\n')[1:-2]:
                if key != '*':
                    if key in i.split(' '):
                        tuples.append((int(i.split(' ')[-1]), float(i.split(' ')[-2]),))
                        tuples.sort()
                        metrics[key] = tuples
                else:
                    if i.split(' ')[0] not in metrics:
                        metrics[i.split(' ')[0]] = [(int(i.split(' ')[-1]), float(i.split(' ')[-2]),)]
                    else:
                        value = metrics.setdefault(i.split(' ')[0])
                        if value[-1][0] == int(i.split(' ')[-1]):
                            value[-1] = (int(i.split(' ')[-1]), float(i.split(' ')[-2]),)
                            value.sort()
                            metrics.update({i.split(' ')[0]: value})
                        else:
                            value.append((int(i.split(' ')[-1]), float(i.split(' ')[-2]),))
                            value.sort()
                            metrics.update({i.split(' ')[0]: value})
        return metrics
