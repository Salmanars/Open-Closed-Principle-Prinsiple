"use client";
import { Card, CardHeader } from "@/components/ui/card";
import { useEffect, useState } from "react";

// Abstraction for UserRepository
interface UserRepository {
  getUsers: () => Promise<User[]>;
}

// Concrete implementation for API repository
export class ApiUserRepository implements UserRepository {
  async getUsers(): Promise<User[]> {
    const response = await fetch("/api/users");
    return await response.json();
  }
}

// New Concrete implementation for Mock repository (Open for Extension)
export class MockUserRepository implements UserRepository {
  async getUsers(): Promise<User[]> {
    return [
      { id: 1, name: "Mock User 1" },
      { id: 2, name: "Mock User 2" },
    ];
  }
}

// Home component using abstraction, without caring about the concrete implementation (Closed for Modification)
const Home = ({ userRepository }: { userRepository: UserRepository }) => {
  const [users, setUsers] = useState<User[]>([]);

  useEffect(() => {
    userRepository.getUsers().then(setUsers);
  }, [userRepository]);

  return (
    <section className="flex flex-col pad-x">
      <div className="text-left">
        <h1 className="text-xl font-bold mb-4">User List</h1>
        <ul className="gap-4 flex flex-col w-full">
          {users.map((user) => (
            <Card key={user.id}>
              <CardHeader>
                <p>{user.name}</p>
              </CardHeader>
            </Card>
          ))}
        </ul>
      </div>
    </section>
  );
};

export default Home;
