using System;
using System.Collections.Generic;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Threading;
public class RelayServer
{
    private TcpListener listener;
    private List<ClientHandler> clients;

    public RelayServer()
    {
        clients = new List<ClientHandler>();
    }

    public void Start(int port)
    {
        listener = new TcpListener(IPAddress.Any, port);
        listener.Start();
        Console.WriteLine("Ретранслятор запущен. Ожидание подключений...");

        while (true)
        {
            TcpClient client = listener.AcceptTcpClient();
            Console.WriteLine("Подключен клиент: " + client.Client.RemoteEndPoint);

            ClientHandler clientHandler = new ClientHandler(client, this);
            clients.Add(clientHandler);

            Thread clientThread = new Thread(clientHandler.HandleClient);
            clientThread.Start();
        }
    }

    public void BroadcastMessage(string message, ClientHandler sender)
    {
        foreach (ClientHandler client in clients)
        {
            if (client != sender)
            {
                client.SendMessage(message);
            }
        }
    }

    public void RemoveClient(ClientHandler client)
    {
        clients.Remove(client);
    }
}
public class ClientHandler
{
    private TcpClient client;
    private RelayServer server;
    private NetworkStream stream;
    private byte[] buffer;

    public ClientHandler(TcpClient client, RelayServer server)
    {
        this.client = client;
        this.server = server;
        buffer = new byte[4096];
    }

    public void HandleClient()
    {
        stream = client.GetStream();

        while (true)
        {
            int bytesRead = stream.Read(buffer, 0, buffer.Length);
            if (bytesRead <= 0)
            {
                server.RemoveClient(this);
                break;
            }

            string message = Encoding.ASCII.GetString(buffer, 0, bytesRead);
            Console.WriteLine("Получено сообщение от клиента: " + message);

            server.BroadcastMessage(message, this);
        }

        client.Close();
    }

    public void SendMessage(string message)
    {
        byte[] bytes = Encoding.ASCII.GetBytes(message);
        stream.Write(bytes, 0, bytes.Length);
    }
}
static void Main(string[] args)
{
    RelayServer server = new RelayServer();
    server.Start(8888);
}
