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
