import type { Express } from "express";
import { createServer, type Server } from "http";
import { storage } from "./storage";
import { api } from "@shared/routes";
import { z } from "zod";

export async function registerRoutes(
  httpServer: Server,
  app: Express
): Promise<Server> {
  
  app.get(api.users.me.path, async (req, res) => {
    const user = await storage.getUser(1);
    
    if (!user) {
      const demoUser = await storage.createUser({
        username: "demo_user",
        password: "password123",
        isAdmin: true // Make the demo user an admin for the owner tools
      });
      const { password, ...safeUser } = demoUser;
      return res.json(safeUser);
    }

    const { password, ...safeUser } = user;
    res.json(safeUser);
  });

  app.get(api.transactions.list.path, async (req, res) => {
    const transactions = await storage.getTransactions();
    res.json(transactions);
  });

  app.post(api.transactions.create.path, async (req, res) => {
    try {
      const input = api.transactions.create.input.parse(req.body);
      const transaction = await storage.createTransaction(input);
      res.status(201).json(transaction);
    } catch (err) {
      if (err instanceof z.ZodError) {
        return res.status(400).json({
          message: err.errors[0].message,
          field: err.errors[0].path.join('.'),
        });
      }
      throw err;
    }
  });

  return httpServer;
}
